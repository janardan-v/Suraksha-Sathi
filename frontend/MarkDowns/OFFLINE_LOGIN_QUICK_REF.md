# Offline Login - Quick Reference Card

## 📱 For Users

### First Time Setup
1. **Login online** with your credentials
2. Credentials **automatically cached**
3. You're ready for offline use!

### Using Offline Login
1. Open app (even without internet)
2. See blue **"Offline Mode"** banner
3. Enter your **same email & password**
4. Click **Login** → Access app offline!

### Important Notes
✅ Works after first online login
✅ Use SAME password as online
✅ Works per device
❌ Won't work if never logged in online
❌ Won't work with wrong password

---

## 💻 For Developers

### Files Modified
- `frontend/api.client.js` - Core auth logic
- `frontend/index.html` - Login UI updates

### Key Functions
```javascript
// Cache credentials after online login
storeOfflineCredentials(email, hash, userData, token)

// Hash password (SHA-256)
await simpleHash(password)

// Handle offline login attempt
await handleOfflineLogin(bodyString)

// Enhanced login (online/offline aware)
await loginWithCaching(loginData)
```

### localStorage Structure
```javascript
offlineAuth: {
  email: string,
  passwordHash: string (SHA-256),
  userData: object,
  token: string (JWT),
  lastSync: number (timestamp)
}
```

### Flow Logic
```javascript
if (isOnline) {
  // Normal server auth + cache credentials
} else {
  // Use cached credentials (local verification)
}
```

---

## 🔒 Security

### What's Stored
- ✅ Email (plain text)
- ✅ Password HASH (SHA-256, one-way)
- ✅ Token (JWT, already encrypted)
- ✅ User data (non-sensitive)
- ❌ Plain text password (NEVER)

### Hash Example
```
Password: "myPassword123"
Hash: "5e884898da28047151d0e56f8dc..."
Length: 64 characters (256 bits)
Reversible: NO
```

---

## 🧪 Testing

### Quick Test
1. Open `offline-auth-test.html`
2. Run "Full Integration Test"
3. Should see: "ALL TESTS PASSED"

### Manual Test
1. Login online → Check console for "[Offline Auth] Credentials cached"
2. Go offline (DevTools → Network → Offline)
3. Login with same credentials → Should work!
4. Try wrong password → Should fail with "Invalid credentials"

---

## 🐛 Troubleshooting

| Problem | Solution |
|---------|----------|
| "No offline login data" | Login online first |
| "Invalid credentials" offline | Use same password as online |
| Button disabled offline | No cached credentials |
| "Token expired" | Login online for fresh token |

---

## 📊 Comparison

| Feature | Before | After |
|---------|--------|-------|
| Offline login | ❌ Not possible | ✅ Works with cache |
| First login | Requires internet | Still requires internet |
| Remote work | ❌ Blocked | ✅ Fully functional |
| Password storage | N/A | ✅ Hashed (secure) |

---

## 📚 Documentation

- **User Guide**: `OFFLINE_AUTH_GUIDE.md`
- **Implementation**: `OFFLINE_AUTH_IMPLEMENTATION.md`
- **FAQ**: `OFFLINE_LOGIN_FAQ.md`
- **Test Suite**: `offline-auth-test.html`

---

## ⚡ Quick Commands

```javascript
// Check if offline auth available
!!localStorage.getItem('offlineAuth')

// Clear cached credentials
localStorage.removeItem('offlineAuth')

// Inspect cached data
JSON.parse(localStorage.getItem('offlineAuth'))

// Check last sync
new Date(JSON.parse(localStorage.getItem('offlineAuth')).lastSync)
```

---

**Status**: ✅ Ready for Use
**Version**: 1.0
**Updated**: December 8, 2025
