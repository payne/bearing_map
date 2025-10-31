# Firebase Setup Instructions

This guide will help you set up Firebase Realtime Database for multi-user collaboration in the Bearing Map application.

## Step 1: Create a Firebase Project

1. Go to the [Firebase Console](https://console.firebase.google.com/)
2. Click "Add project" or "Create a project"
3. Enter a project name (e.g., "bearing-map")
4. Follow the setup wizard (you can disable Google Analytics if not needed)
5. Click "Create project"

## Step 2: Create a Realtime Database

1. In your Firebase project, click "Realtime Database" in the left sidebar
2. Click "Create Database"
3. Choose a location (select the one closest to your users)
4. Start in **Test mode** for now (we'll configure security rules next)
5. Click "Enable"

## Step 3: Configure Database Security Rules

1. In the Realtime Database page, click the "Rules" tab
2. Replace the default rules with these:

```json
{
  "rules": {
    "bearingPoints": {
      ".read": true,
      ".write": true,
      "$pointId": {
        ".validate": "newData.hasChildren(['lat', 'lon', 'bearing', 'isReflection', 'timestamp'])"
      }
    }
  }
}
```

3. Click "Publish"

**Note**: These rules allow anyone to read and write. For production use, consider adding authentication.

## Step 4: Get Your Firebase Configuration

1. Click the gear icon next to "Project Overview" in the left sidebar
2. Select "Project settings"
3. Scroll down to "Your apps" section
4. Click the web icon `</>` to add a web app
5. Give your app a nickname (e.g., "bearing-map-web")
6. Click "Register app"
7. Copy the `firebaseConfig` object

## Step 5: Update index.html

1. Open `index.html` in a text editor
2. Find the Firebase Configuration section (around line 100)
3. Replace the placeholder values with your actual Firebase config:

```javascript
const firebaseConfig = {
    apiKey: "YOUR_ACTUAL_API_KEY",
    authDomain: "your-project-id.firebaseapp.com",
    databaseURL: "https://your-project-id-default-rtdb.firebaseio.com",
    projectId: "your-project-id",
    storageBucket: "your-project-id.appspot.com",
    messagingSenderId: "YOUR_ACTUAL_SENDER_ID",
    appId: "YOUR_ACTUAL_APP_ID"
};
```

4. Save the file

## Step 6: Test the Application

1. Open `index.html` in a web browser
2. Open the browser's Developer Console (F12) to check for any errors
3. Add a point to the map
4. Open `index.html` in a different browser or incognito window
5. You should see the point automatically appear on both maps!

## How It Works

- **Adding points**: When a user adds a point, it's saved to Firebase and automatically appears on all connected users' maps
- **Deleting points**: Right-click a point and select "Delete Point" - it will be removed for all users
- **Reflection toggle**: Right-click a point and toggle reflection status - the change syncs to all users
- **Real-time sync**: All changes are pushed to Firebase and distributed to all connected clients instantly

## Troubleshooting

### Points not syncing

1. Check browser console for errors
2. Verify your Firebase configuration is correct
3. Ensure your database URL includes your project ID
4. Check that database rules allow read/write access

### "Permission denied" errors

1. Go to Firebase Console > Realtime Database > Rules
2. Verify rules allow read and write access
3. Make sure you clicked "Publish" after updating rules

### Database URL issues

- Make sure the `databaseURL` ends with `.firebaseio.com`
- It should include your project ID, like: `https://your-project-id-default-rtdb.firebaseio.com`

## Optional: Add Authentication

For production use, you may want to add Firebase Authentication to control who can access the map:

1. Enable Firebase Authentication in your project
2. Update the security rules to require authentication
3. Add login UI to the application
4. Initialize Firebase Auth before accessing the database

See [Firebase Authentication documentation](https://firebase.google.com/docs/auth/web/start) for details.

## Data Structure

The application stores points in Firebase with this structure:

```
bearingPoints/
  ├── -UniqueId1/
  │   ├── lat: 40.123456
  │   ├── lon: -100.654321
  │   ├── bearing: 45.0
  │   ├── isReflection: false
  │   └── timestamp: 1234567890
  ├── -UniqueId2/
  │   └── ...
```

Each point has a unique Firebase-generated ID and contains the coordinates, bearing, reflection status, and timestamp.
