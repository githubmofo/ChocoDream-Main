# Firebase Setup Guide for ChocoDream

This guide will help you set up Firebase for your ChocoDream application.

## Prerequisites

1. A Google account
2. Access to Firebase Console (https://console.firebase.google.com/)

## Step 1: Create a Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Add project" or "Create a project"
3. Enter your project name (e.g., "ChocoDream")
4. Follow the setup wizard:
   - Disable Google Analytics (optional)
   - Click "Create project"
5. Wait for the project to be created

## Step 2: Enable Authentication

1. In your Firebase project, go to **Authentication** in the left sidebar
2. Click **Get started**
3. Click on the **Sign-in method** tab
4. Enable **Email/Password**:
   - Click on "Email/Password"
   - Toggle "Enable" to ON
   - Click "Save"

## Step 3: Create Realtime Database

1. In your Firebase project, go to **Realtime Database** in the left sidebar
2. Click **Create Database**
3. Choose a location (select the closest region to your users)
4. Choose **Start in test mode** (for development)
   - **Important**: For production, you'll need to set up proper security rules
5. Click "Enable"

## Step 4: Get Your Firebase Configuration

1. In your Firebase project, click the gear icon ⚙️ next to "Project Overview"
2. Select **Project settings**
3. Scroll down to "Your apps" section
4. Click the **Web icon** `</>` to add a web app
5. Register your app:
   - Enter an app nickname (e.g., "ChocoDream Web")
   - You can skip Firebase Hosting for now
   - Click "Register app"
6. Copy the Firebase configuration object (it looks like this):

```javascript
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "your-project.firebaseapp.com",
  databaseURL: "https://your-project-default-rtdb.firebaseio.com",
  projectId: "your-project-id",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

## Step 5: Update firebase-app.js

1. Open `js/firebase-app.js`
2. Find the `firebaseConfig` object (around line 15)
3. Replace the placeholder values with your actual Firebase configuration:

```javascript
const firebaseConfig = {
    apiKey: "YOUR_ACTUAL_API_KEY",
    authDomain: "YOUR_ACTUAL_AUTH_DOMAIN",
    databaseURL: "YOUR_ACTUAL_DATABASE_URL",
    projectId: "YOUR_ACTUAL_PROJECT_ID",
    storageBucket: "YOUR_ACTUAL_STORAGE_BUCKET",
    messagingSenderId: "YOUR_ACTUAL_MESSAGING_SENDER_ID",
    appId: "YOUR_ACTUAL_APP_ID"
};
```

## Step 6: Set Up Database Security Rules (Important!)

1. Go to **Realtime Database** in Firebase Console
2. Click on the **Rules** tab
3. For development, you can use these rules (NOT for production):

```json
{
  "rules": {
    ".read": "auth != null",
    ".write": "auth != null",
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
        ".read": "$uid === auth.uid",
        ".write": "$uid === auth.uid"
      }
    }
  }
}
```

4. Click "Publish"

**⚠️ Warning**: The above rules allow anyone to read/write orders and messages. For production, implement proper security rules based on your requirements.

## Step 7: Test Your Setup

1. Open your website in a browser
2. Try to sign up a new user:
   - Go to the login page
   - Click "Sign Up"
   - Enter name, email, and password
   - Submit the form
3. Check Firebase Console:
   - Go to **Authentication** → **Users** tab
   - You should see your new user
4. Try to place an order:
   - Add items to cart
   - Go to checkout
   - Fill in the form and submit
5. Check Firebase Console:
   - Go to **Realtime Database**
   - You should see the order data under `orders/`
6. Try to send a contact message:
   - Go to the contact page
   - Fill in the form and submit
7. Check Firebase Console:
   - Go to **Realtime Database**
   - You should see the message under `contactMessages/`

## Database Structure

Your Firebase Realtime Database will have the following structure:

```
chocodream-db/
├── orders/
│   └── ORD1234567890/
│       ├── orderId: "ORD1234567890"
│       ├── orderNumber: "CD1234567890"
│       ├── userId: "user-uid"
│       ├── userEmail: "user@example.com"
│       ├── items: { ... }
│       ├── customer: { ... }
│       ├── payment: { ... }
│       ├── orderDate: timestamp
│       └── status: "pending"
├── contactMessages/
│   └── MSG1234567890/
│       ├── messageId: "MSG1234567890"
│       ├── userId: "user-uid"
│       ├── name: "John Doe"
│       ├── email: "john@example.com"
│       ├── phone: "1234567890"
│       ├── message: "Hello..."
│       ├── timestamp: timestamp
│       ├── status: "new"
│       └── read: false
└── users/
    └── user-uid/
        ├── name: "John Doe"
        ├── email: "john@example.com"
        ├── createdAt: timestamp
        ├── orders/
        └── messages/
```

## Troubleshooting

### Error: "Firebase: Error (auth/network-request-failed)"
- Check your internet connection
- Verify your Firebase configuration is correct
- Make sure Firebase services are enabled in your project

### Error: "Firebase: Error (auth/invalid-api-key)"
- Double-check your API key in `firebase-app.js`
- Make sure you copied the entire configuration object correctly

### Error: "Permission denied" when saving data
- Check your database security rules
- Make sure you're authenticated (for protected routes)
- Verify the database URL is correct

### Orders/Messages not appearing in database
- Check browser console for errors
- Verify database rules allow writes
- Make sure the database is created and active

## Production Considerations

Before deploying to production:

1. **Set up proper security rules** - Don't allow public read/write access
2. **Enable authentication** - Require users to sign in for sensitive operations
3. **Set up domain restrictions** - Restrict API keys to your domain
4. **Enable billing** - Firebase has a free tier, but monitor usage
5. **Set up monitoring** - Use Firebase Analytics and Crashlytics
6. **Backup data** - Regularly export your database

## Support

If you encounter any issues:
1. Check the Firebase Console for error messages
2. Check browser console for JavaScript errors
3. Verify all configuration values are correct
4. Review Firebase documentation: https://firebase.google.com/docs

