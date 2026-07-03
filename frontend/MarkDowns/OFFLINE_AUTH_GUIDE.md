# Offline Authentication - User Guide

## Overview

The app now supports **offline login** using cached credentials from your previous successful login. This means you can access the app even without internet connection!

## How It Works

### First Login (Online Required)

1. **Connect to Internet**: Your first login requires internet connection
2. **Login Normally**: Enter your email and password
3. **Credentials Cached**: Your login info is securely stored locally
4. **Ready for Offline**: You can now login offline anytime

### Subsequent Logins (Works Offline!)

1. **No Internet? No Problem**: Open the app even when offline
2. **See Offline Mode Banner**: Blue banner shows "Using cached credentials"
3. **Login with Same Credentials**: Use the SAME email and password
4. **Instant Access**: Login works immediately using local verification

## What's Stored Locally

For offline login to work, the following is cached after successful online login:

✅ **Email Address**: Your login email
✅ **Password Hash**: SHA-256 encrypted password (NOT plain text)
✅ **User Profile**: Name, role, phone, preferences
✅ **Access Token**: Authentication token for API requests
✅ **Last Sync Time**: When credentials were last updated

**Security Note**: Your actual password is NEVER stored. Only a one-way hash is kept for verification.

## Login Flow

### Online Login
```
1. Enter email & password
2. Server validates credentials
3. Server generates access token
4. Credentials cached locally (hashed)
5. Login successful → Redirect to home
```

### Offline Login
```
1. Enter email & password
2. Check cached credentials exist
3. Verify email matches cached email
4. Hash entered password
5. Compare with cached hash
6. If match → Login successful with cached token
7. Redirect to home (offline mode)
```

## Visual Indicators

### Online Mode
- No banner shown
- Login button: Normal state
- Success message: "Login successful!"

### Offline Mode
- **Blue banner**: "Offline Mode - Using cached credentials"
- Login button: Enabled (if credentials cached)
- Success message: "Logged in offline mode!"

### No Cached Credentials
- Blue banner: "Offline Mode"
- Login button: **Disabled**
- Error if attempted: "No offline login data available"

## Security Features

### Password Protection
- Passwords hashed using **SHA-256** algorithm
- Original password never stored
- Hash comparison happens locally
- No password transmission when offline

### Token Security
- Access token encrypted in localStorage
- Auto-expires based on server settings
- Cleared on logout
- Synced on next online login

### Credential Updates
- Credentials re-cached on every successful online login
- Old tokens replaced with fresh ones
- Password changes require online connection
- Auto-cleanup on logout

## Limitations

### What Works Offline (After Cached Login)
✅ View all pages
✅ Submit hazard reports (queued)
✅ Record attendance (queued)
✅ Complete checklists (queued)
✅ Upload media (stored locally)
✅ Browse cached data

### What Requires Internet
❌ First-time login (no cached credentials)
❌ Registration/signup
❌ Password changes
❌ Fetching new data from server
❌ Profile updates
❌ Real-time sync
❌ Push notifications

## Troubleshooting

### "No offline login data available"

**Problem**: Trying to login offline but no credentials cached
**Solution**: 
1. Connect to internet
2. Login once online
3. Credentials will be cached
4. Can now login offline

### "Invalid credentials" when offline

**Problem**: Password doesn't match cached password
**Solutions**:
- Ensure you're using the SAME password as last online login
- Check Caps Lock is off
- Verify email is correct (case-insensitive)
- If password was changed online on another device, you must login online once to update cache

### Login button disabled when offline

**Problem**: No cached credentials available
**Solution**: Must login online first to cache credentials

### "Token expired" error after offline login

**Problem**: Cached token is too old
**Solution**:
- Token expiration set by server (usually 7-30 days)
- Connect to internet and login to refresh token
- New token automatically cached

## Best Practices

### For Daily Use

1. **Login Online Regularly**: Refreshes your token and data
2. **Same Device**: Offline login works per device (credentials cached locally)
3. **Keep Password Consistent**: Don't change password and expect offline login with old password
4. **Sync When Possible**: Connect to internet periodically to sync queued data

### For Field Workers

1. **Pre-Cache Before Shift**: Login while at facility (with WiFi)
2. **Work Offline**: Use app in remote areas without signal
3. **Return and Sync**: When back in coverage, app auto-syncs all data
4. **Daily Online Login**: Start each day with online login for fresh data

### For Security

1. **Don't Share Devices**: Each person should use their own device
2. **Logout When Done**: Clears cached credentials
3. **Screen Lock**: Protect your device with password/biometric
4. **Report Lost Devices**: Inform admin to revoke tokens

## Advanced Features

### Automatic Credential Sync

When you login online:
- Old cached credentials replaced
- New token stored
- User data updated
- Timestamp refreshed

### Multi-Device Support

Each device stores its own cached credentials:
- Login on Device A → Cached on Device A only
- Login on Device B → Cached on Device B only
- Both work independently offline

### Credential Expiry

Cached credentials remain valid until:
- User logs out
- Token expires (server-side setting)
- User changes password online
- Manual cache clear

## Technical Details

### Storage Location
- **localStorage**: `offlineAuth` key
- **Contains**: email, passwordHash, userData, token, lastSync

### Encryption
- **Password**: SHA-256 hash (256-bit)
- **Token**: Stored as-is (already JWT encrypted by server)
- **User Data**: JSON string

### Hash Algorithm
```javascript
SHA-256(password) → 64-character hex string
Example: "5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8"
```

### Verification Process
```javascript
1. Get cached hash from localStorage
2. Hash entered password with SHA-256
3. Compare: entered_hash === cached_hash
4. If match → Grant access
5. If no match → Deny access
```

## Privacy Notes

- Credentials stored only on your device
- No cloud backup of cached credentials
- Cleared when you clear browser data
- Not accessible by other websites
- Removed on app uninstall

## FAQ

**Q: Is offline login secure?**
A: Yes! Passwords are hashed (not stored), tokens are encrypted, and everything stays on your device only.

**Q: Can someone else login using my cached credentials?**
A: No, they still need your password. The hash is useless without the original password.

**Q: What happens if I forget my password?**
A: You'll need internet to reset password. Offline login won't work with wrong password.

**Q: Can I login offline on multiple devices?**
A: Yes, but you must login online once on each device to cache credentials.

**Q: How long do cached credentials last?**
A: Until you logout, token expires, or you clear browser data. Token typically valid 7-30 days.

**Q: Does offline login work in incognito/private mode?**
A: No, credentials aren't cached in private browsing modes.

**Q: Can I disable offline login?**
A: Yes, simply logout. This clears all cached credentials.

**Q: What if my password is changed by admin?**
A: Offline login will fail. You must login online with new password to update cache.

## Updates & Maintenance

### Credential Refresh Recommended
- **Daily**: For active field workers
- **Weekly**: For regular users
- **Monthly**: Minimum to keep token fresh

### Cache Management
- Auto-cleaned on logout
- Can manually clear browser storage
- Re-cache by logging in online

### Version Compatibility
- Works across app updates
- Token format changes may require re-login
- Always backward compatible

## Support

If you experience issues:
1. Try logging in online first
2. Check browser console for errors
3. Clear browser cache and retry
4. Contact IT support with error message

---

**Remember**: Offline login is a convenience feature. For best security and latest data, login online whenever possible!
