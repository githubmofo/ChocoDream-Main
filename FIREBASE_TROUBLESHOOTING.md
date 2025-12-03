# Firebase Troubleshooting Guide

If data is not being saved to Firebase, follow these steps:

## Step 1: Check Browser Console

1. Open your website in a browser
2. Press `F12` to open Developer Tools
3. Go to the **Console** tab
4. Try to sign up or place an order
5. Look for error messages starting with "Firebase:"

### Common Console Errors:

- **"Permission denied"** → Database rules are blocking writes
- **"auth/operation-not-allowed"** → Email/Password authentication not enabled
- **"Network request failed"** → Check internet connection or Firebase service status
- **"Database not found"** → Realtime Database not created

## Step 2: Verify Firebase Setup

### A. Check Authentication is Enabled

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Select your project: **chocodream-30b73**
3. Click **Authentication** in the left sidebar
4. Click **Sign-in method** tab
5. Verify **Email/Password** is enabled (should show "Enabled")
6. If not enabled:
   - Click on "Email/Password"
   - Toggle "Enable" to ON
   - Click "Save"

### B. Check Realtime Database Exists

1. In Firebase Console, click **Realtime Database** in the left sidebar
2. If you see "Get started" or "Create Database":
   - Click "Create Database"
   - Choose a location (e.g., `asia-south1` for India)
   - Choose **"Start in test mode"** (for development)
   - Click "Enable"

### C. Check Database Rules

1. In **Realtime Database**, click the **Rules** tab
2. For development/testing, use these rules:

```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

3. Click **Publish**

⚠️ **WARNING**: These rules allow anyone to read/write. Only use for development!

### D. For Production (More Secure Rules):

```json
{
  "rules": {
    "orders": {
      ".read": true,
      ".write": true
    },
    "contactMessages": {
      ".read": true,
      ".write": true
    },
    "users": {
      "$uid": {
        ".read": "$uid === auth.uid || auth != null",
        ".write": "$uid === auth.uid || auth != null"
      }
    }
  }
}
```

## Step 3: Test Authentication

1. Open `login.html` in your browser
2. Open Browser Console (F12)
3. Try to sign up with a new account
4. Check console for messages:
   - Should see: "Firebase: Starting sign up process..."
   - Should see: "Firebase: User created successfully"
   - Should see: "Firebase: User data saved to database successfully"

5. Check Firebase Console:
   - Go to **Authentication** → **Users** tab
   - You should see the new user

## Step 4: Test Database Writes

1. After signing up, check **Realtime Database**:
   - You should see a `users` node
   - Click on it to see your user data

2. Try placing an order:
   - Add items to cart
   - Go to checkout
   - Fill the form and submit
   - Check console for "Firebase: Order saved to database successfully"
   - Check **Realtime Database** for `orders` node

3. Try sending a contact message:
   - Go to contact page
   - Fill the form and submit
   - Check console for "Firebase: Message saved to database successfully"
   - Check **Realtime Database** for `contactMessages` node

## Step 5: Common Issues and Solutions

### Issue: "Permission denied" error

**Solution:**
1. Go to Realtime Database → Rules
2. Set rules to allow writes (see Step 2C above)
3. Click "Publish"
4. Try again

### Issue: "auth/operation-not-allowed"

**Solution:**
1. Go to Authentication → Sign-in method
2. Enable Email/Password
3. Try again

### Issue: No data appears in database

**Possible causes:**
1. Database rules blocking writes → Fix rules (Step 2C)
2. Database not created → Create database (Step 2B)
3. Wrong database URL → Check `firebase-app.js` line 15
4. JavaScript errors → Check browser console

### Issue: Users created but not in database

**Solution:**
- This is OK! User authentication is separate from database
- Check if database rules allow writes to `users/{uid}`
- Check console for "Firebase: Failed to save user data to database" warning

## Step 6: Verify Configuration

Check `js/firebase-app.js` lines 12-21:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyDi-N6RzczdoNvMalpX9O0v6vz7JvvaQ_4",
  authDomain: "chocodream-30b73.firebaseapp.com",
  databaseURL: "https://chocodream-30b73-default-rtdb.firebaseio.com",
  projectId: "chocodream-30b73",
  storageBucket: "chocodream-30b73.firebasestorage.app",
  messagingSenderId: "1072238144915",
  appId: "1:1072238144915:web:3d716100e74a32d1c98c66",
  measurementId: "G-VNG183RB9Q"
};
```

Make sure:
- `databaseURL` matches your Realtime Database URL
- All values are correct (no typos)
- No extra spaces or quotes

## Step 7: Test with Simple Write

Open browser console and run:

```javascript
import { database } from './js/firebase-app.js';
import { ref, set } from 'https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js';

// Test write
const testRef = ref(database, 'test/write');
await set(testRef, { message: 'Test successful', timestamp: Date.now() });
console.log('Test write successful!');
```

If this works, check your database rules. If it fails, check the error message.

## Still Not Working?

1. **Check Firebase Status**: https://status.firebase.google.com/
2. **Check Browser Console**: Look for any red error messages
3. **Check Network Tab**: 
   - Open Developer Tools → Network tab
   - Try to sign up or save data
   - Look for failed requests to `firebaseio.com`
   - Check the error response

4. **Verify Database URL**:
   - Go to Firebase Console → Realtime Database
   - Copy the database URL
   - Compare with `databaseURL` in `firebase-app.js`

5. **Clear Browser Cache**:
   - Press `Ctrl+Shift+Delete` (Windows) or `Cmd+Shift+Delete` (Mac)
   - Clear cached files
   - Refresh the page

## Quick Checklist

- [ ] Authentication → Email/Password is enabled
- [ ] Realtime Database is created
- [ ] Database rules allow writes (for testing: `".write": true`)
- [ ] `databaseURL` in `firebase-app.js` is correct
- [ ] No JavaScript errors in browser console
- [ ] Internet connection is working
- [ ] Firebase services are online (check status page)

## Need More Help?

1. Check the browser console for specific error messages
2. Check Firebase Console for any error notifications
3. Review Firebase documentation: https://firebase.google.com/docs
4. Check the error codes in `getAuthErrorMessage()` function in `firebase-app.js`

