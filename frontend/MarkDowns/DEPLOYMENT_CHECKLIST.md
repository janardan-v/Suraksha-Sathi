# Offline-First Deployment Checklist

## Pre-Deployment Verification

### ✅ File Verification
- [x] `frontend/sw.js` - Service Worker created
- [x] `frontend/offline-db.js` - IndexedDB manager created
- [x] `frontend/offline-ui.js` - UI indicators created
- [x] `frontend/api.client.js` - Enhanced with offline support
- [x] `frontend/manifest.json` - PWA configuration updated
- [x] `frontend/index.html` - SW registration added
- [x] `frontend/features_pages/updated-home-page.html` - Offline support added
- [x] `frontend/offline-test.html` - Testing page created
- [x] Documentation files created

### 🧪 Local Testing (Required Before Deploy)

#### Test 1: Service Worker Registration
```
[ ] Open browser console
[ ] Navigate to your app
[ ] Check for: "[SW] Service Worker registered"
[ ] No errors in console
```

#### Test 2: Offline Mode
```
[ ] Open DevTools (F12)
[ ] Go to Network tab
[ ] Select "Offline"
[ ] Navigate between pages - should work
[ ] Submit a hazard report - should queue
[ ] Check offline banner appears
```

#### Test 3: Online Sync
```
[ ] While still offline, submit test data
[ ] Go back online (Network → No throttling)
[ ] Verify auto-sync happens
[ ] Check sync indicator appears
[ ] Verify success toast shows
```

#### Test 4: IndexedDB
```
[ ] DevTools → Application → IndexedDB
[ ] Verify "SurakshaSathiDB" exists
[ ] Check object stores created:
    [ ] offlineQueue
    [ ] hazardReports
    [ ] attendance
    [ ] checklists
    [ ] cachedData
    [ ] mediaFiles
```

#### Test 5: Cache Storage
```
[ ] DevTools → Application → Cache Storage
[ ] Verify caches exist:
    [ ] suraksha-sathi-v1
    [ ] suraksha-runtime-v1
    [ ] suraksha-api-v1
[ ] Check cached files present
```

#### Test 6: PWA Install
```
[ ] Open in Chrome/Edge
[ ] Look for install prompt
[ ] Click install
[ ] Verify app opens standalone
[ ] Test offline functionality in installed app
```

## Deployment Steps

### Step 1: Server Requirements
```
[ ] Ensure server supports HTTPS (required for Service Workers)
[ ] Configure proper MIME types:
    - .js  → application/javascript
    - .json → application/json
    - .html → text/html
[ ] Enable gzip compression for better performance
```

### Step 2: Upload Files
```
[ ] Upload all frontend files to server
[ ] Maintain directory structure
[ ] Verify file permissions (readable by web server)
[ ] Check file paths are correct in sw.js
```

### Step 3: Configure Headers (Important!)
```
[ ] Service Worker file (sw.js):
    Cache-Control: max-age=0, no-cache, no-store, must-revalidate
    
[ ] Static assets (HTML, CSS, JS):
    Cache-Control: public, max-age=31536000, immutable
    
[ ] manifest.json:
    Cache-Control: public, max-age=86400
```

### Step 4: Update Configuration
```
[ ] Update API_BASE_URL in api.client.js to production URL
[ ] Verify all paths in sw.js STATIC_ASSETS are correct
[ ] Update manifest.json start_url if needed
[ ] Check icon paths in manifest.json
```

### Step 5: Test on Production
```
[ ] Clear browser cache
[ ] Open production URL
[ ] Verify SW registers: DevTools → Application → Service Workers
[ ] Test offline mode on production
[ ] Test on mobile devices
[ ] Test PWA installation
```

## Post-Deployment Testing

### Desktop Testing
```
Chrome:
[ ] Service Worker active
[ ] Offline mode works
[ ] PWA installs
[ ] Sync works
[ ] Cache updates

Firefox:
[ ] Service Worker active
[ ] Offline mode works
[ ] Cache works
[ ] Sync works

Edge:
[ ] Service Worker active
[ ] Offline mode works
[ ] PWA installs
[ ] Sync works
```

### Mobile Testing
```
Chrome Android:
[ ] Service Worker active
[ ] Offline mode works
[ ] Add to home screen works
[ ] Standalone mode works
[ ] Background sync works

Safari iOS:
[ ] Service Worker active
[ ] Offline mode works
[ ] Add to home screen works
[ ] Standalone mode works
[ ] Basic functionality works
```

## Monitoring & Maintenance

### Monitor These Metrics
```
[ ] Service Worker registration rate
[ ] Offline queue usage
[ ] Storage quota usage
[ ] Sync success/failure rate
[ ] PWA install rate
[ ] Error logs
```

### Regular Maintenance
```
Weekly:
[ ] Check error logs
[ ] Monitor storage usage
[ ] Review failed sync requests

Monthly:
[ ] Update service worker version
[ ] Clear old caches
[ ] Review and optimize cached assets
[ ] Test offline functionality

Quarterly:
[ ] Review storage limits
[ ] Update documentation
[ ] Test on new browser versions
[ ] Optimize cache strategy
```

## Rollback Plan

### If Issues Occur
```
1. Unregister Service Worker:
   navigator.serviceWorker.getRegistrations()
     .then(regs => regs.forEach(reg => reg.unregister()));

2. Clear caches:
   caches.keys().then(keys => 
     Promise.all(keys.map(key => caches.delete(key)))
   );

3. Update sw.js with fix

4. Increment CACHE_NAME version:
   const CACHE_NAME = 'suraksha-sathi-v2';

5. Users will get update on next page load
```

## Troubleshooting Guide

### Service Worker Not Updating
```
Problem: Changes to sw.js not reflecting
Solution:
1. DevTools → Application → Service Workers
2. Check "Update on reload"
3. Click "Unregister"
4. Hard refresh (Ctrl+Shift+R)
5. Increment cache version
```

### Quota Exceeded Error
```
Problem: Storage quota exceeded
Solution:
1. Implement cache cleanup
2. Reduce cached assets
3. Lower cache TTL
4. Clear old data periodically
```

### Sync Not Working
```
Problem: Offline queue not syncing
Solution:
1. Check online status: navigator.onLine
2. Verify background sync permission
3. Check console for sync errors
4. Manually trigger: syncOfflineQueue()
```

### PWA Not Installing
```
Problem: Install prompt not showing
Solution:
1. Verify HTTPS
2. Check manifest.json valid
3. Ensure all icons exist
4. Check manifest linked in HTML
5. Service Worker must be registered
```

## Performance Optimization

### After Deployment
```
[ ] Enable HTTP/2 on server
[ ] Implement resource hints:
    <link rel="preconnect" href="API_URL">
    <link rel="dns-prefetch" href="API_URL">
[ ] Optimize images (WebP format)
[ ] Minify JavaScript files
[ ] Enable Brotli compression
[ ] Implement CDN for static assets
```

## Security Checklist

```
[ ] HTTPS enforced
[ ] CSP headers configured
[ ] No sensitive data in cache
[ ] Token refresh mechanism
[ ] Secure IndexedDB access
[ ] Input validation on cached data
```

## Success Criteria

### Minimum Requirements
```
[ ] Service Worker registers on all supported browsers
[ ] App loads offline (cached pages)
[ ] Offline queue works (requests queued when offline)
[ ] Auto-sync works (syncs when back online)
[ ] PWA installable
[ ] No console errors
[ ] Mobile responsive
```

### Optimal Performance
```
[ ] Page load < 2 seconds (cached)
[ ] Offline mode instant
[ ] Sync latency < 5 seconds
[ ] Storage usage < 50MB
[ ] Zero data loss
[ ] 99% sync success rate
```

## Documentation

### Ensure Available
```
[ ] OFFLINE_GUIDE.md - Comprehensive guide
[ ] OFFLINE_IMPLEMENTATION_SUMMARY.md - Implementation details
[ ] QUICK_REFERENCE.txt - Quick reference card
[ ] This deployment checklist
[ ] Code comments in all files
```

## Support Resources

### For Users
```
- Offline indicator shows status
- Clear error messages
- Toast notifications for feedback
- Install instructions visible
```

### For Developers
```
- Console logs for debugging
- offline-test.html for testing
- Complete documentation
- Event listeners for monitoring
```

---

## Sign-Off

```
Tested by: _____________________  Date: __________

Deployed by: ____________________  Date: __________

Verified by: ____________________  Date: __________
```

---

**Note:** This checklist should be completed before and after every deployment.
Keep a copy in your deployment documentation for reference.

Good luck with your deployment! 🚀
