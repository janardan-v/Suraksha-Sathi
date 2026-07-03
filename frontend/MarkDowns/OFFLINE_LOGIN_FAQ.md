# Offline Login - Important Information

## ✨ NEW: Offline Login Now Supported!

**Great news!** You can now login even without internet connection using cached credentials from your previous successful login.

## How Offline Login Works

### First Login (Requires Internet)
1. **Connect to Internet**: Your first login requires internet connection
2. **Login Normally**: Enter your email and password
3. **Credentials Automatically Cached**: Your login info is securely stored locally (password is hashed, never stored in plain text)
4. **You're Ready**: You can now login offline anytime with the same credentials

### Subsequent Logins (Works Offline!)
1. **No Internet? No Problem**: Open the app even when offline
2. **See Offline Mode Banner**: Blue banner shows "Using cached credentials to login"
3. **Login with Same Credentials**: Use the SAME email and password you used online
4. **Instant Access**: Login works immediately using local verification

## What You Can Do Offline

Once you're logged in, you can use these features **without internet**:

✅ **View Pages**: Browse all cached pages (home, reports, checklists, etc.)
✅ **Submit Hazard Reports**: Reports are saved locally and auto-sync when online
✅ **Record Attendance**: Attendance data queued for later sync
✅ **Complete Checklists**: Work offline, sync automatically
✅ **Upload Media**: Photos and audio recordings stored locally
✅ **View Cached Data**: See previously loaded reports and data

## What Requires Internet Connection

❌ **Login/Signup**: Must authenticate with server
❌ **Logout**: Invalidates server session token
❌ **Password Changes**: Security-critical operation
❌ **Fetching New Data**: Latest reports, notifications, updates
❌ **Push Notifications**: Real-time alerts require network
❌ **Profile Updates**: Changes to account information

## How Offline Mode Works

### When You Go Offline:

1. **Orange Banner Appears**: Shows "You're offline" at top of pages
2. **Requests Queued**: Data submissions saved locally with timestamp
3. **Sync Counter**: Shows number of pending actions (e.g., "3 items")
4. **Auto-Save**: Everything stored in IndexedDB (persistent storage)

### When You Come Back Online:

1. **Green Banner**: Shows "Back online! Syncing..."
2. **Auto-Sync**: All queued requests sent to server automatically
3. **Toast Notifications**: Confirms successful sync
4. **Data Refresh**: New information loaded from server

## Troubleshooting

### "Reading 'accessToken' undefined" Error

**Problem**: You tried to login while offline
**Solution**: Check your internet connection and try again

### Can't Login - Offline Warning Shows

**Problem**: No internet connection detected
**Solution**: 
- Check WiFi/mobile data is enabled
- Verify network connection working
- Wait for connection, then refresh page
- Login button disabled until online

### Stuck on "Logging in..."

**Problem**: Network connection unstable during login
**Solution**:
- Wait for timeout (30 seconds)
- Check error message in toast notification
- Ensure stable internet connection
- Try again

### Data Not Syncing When Online

**Problem**: Queue not processing automatically
**Solution**:
- Check console for sync errors
- Look for sync indicator spinning icon
- Manual refresh may trigger sync
- Check browser DevTools > Application > IndexedDB

## Tips for Best Experience

1. **Login Before Going to Site**: If you know you'll be working in areas with poor signal, login while you have good internet

2. **Let Sync Complete**: When you come back online, wait for the sync indicator to finish before closing the app

3. **Check Queue Counter**: The offline banner shows how many items are waiting to sync

4. **Enable Push Notifications**: Get alerts when back online about sync status

5. **Update When Online**: Keep app up-to-date by refreshing when you have good connection

## Technical Details

### Cached Assets
- All HTML pages
- JavaScript files
- CSS stylesheets  
- Icons and images
- Service worker

### Stored Data
- Offline request queue
- Hazard reports (with media)
- Attendance records
- Checklist submissions
- Cached API responses
- Media files (photos, audio)

### Storage Limits
- **IndexedDB**: ~50MB - 100MB+ (browser dependent)
- **Cache Storage**: ~50MB+ per origin
- **Automatic Cleanup**: Old cached data removed automatically

### Browser Support
✅ Chrome/Edge (Desktop & Mobile)
✅ Firefox (Desktop & Mobile)
✅ Safari (iOS 11.3+)
✅ Opera
❌ Internet Explorer (not supported)

## Security Notes

- **Tokens Encrypted**: Access tokens stored securely in localStorage
- **Auto-Logout**: Invalid/expired tokens cleared automatically
- **Offline Data**: Stored locally only on your device
- **Sync Security**: All synced data sent over HTTPS
- **No Password Storage**: Passwords never stored locally

## Questions?

**Q: Will my queued data be lost if I close the browser?**
A: No! Data stored in IndexedDB persists even when browser closed.

**Q: How long will queued requests wait?**
A: Indefinitely until synced successfully or manually cleared.

**Q: Can I work for days offline then sync?**
A: Yes! Queue unlimited items. Sync when convenient.

**Q: What if server rejects queued data?**
A: Failed items remain in queue. Check sync errors in console.

**Q: Does offline mode use data/bandwidth?**
A: No data usage when offline. Syncing uses minimal bandwidth.

## Learn More

- See `OFFLINE_GUIDE.md` for complete offline documentation
- Check `OFFLINE_IMPLEMENTATION_SUMMARY.md` for technical details
- Review `DEPLOYMENT_CHECKLIST.md` for setup instructions
