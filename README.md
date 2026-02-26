# 🛡️ Disaster Awareness & Route Planning System

A full-stack React + Firebase application for real-time disaster alerts,
shelter routing, admin management, and push notifications.

---

## 📁 Folder Structure

```
disaster-awareness-system/
├── public/
│   ├── index.html
│   └── firebase-messaging-sw.js      ← FCM Service Worker
├── src/
│   ├── firebase/
│   │   ├── config.js                 ← Firebase SDK init
│   │   └── messaging.js              ← FCM token & foreground handler
│   ├── context/
│   │   └── AuthContext.jsx           ← Auth state + login/register/logout
│   ├── components/
│   │   ├── PrivateRoute.jsx          ← Route guards (user + admin)
│   │   ├── Navbar.jsx                ← Top navigation
│   │   └── AlertCard.jsx             ← Disaster alert card with severity
│   ├── hooks/
│   │   └── useAlerts.js              ← Firestore real-time hooks
│   ├── pages/
│   │   ├── Login.jsx
│   │   ├── Register.jsx
│   │   ├── Dashboard.jsx             ← Active alerts + filters
│   │   ├── RoutePlanner.jsx          ← Google Maps + safe routing
│   │   └── AdminPanel.jsx            ← CRUD alerts & shelters
│   └── App.jsx                       ← Routes
├── functions/
│   ├── src/
│   │   └── index.ts                  ← Cloud Function: FCM on alert created
│   └── package.json
├── firestore.rules                   ← Security rules
├── firebase.json                     ← Hosting + Firestore + Functions config
├── .firebaserc                       ← Project alias
└── package.json
```

---

## 🚀 Setup Guide

### 1. Create Firebase Project
1. Go to https://console.firebase.google.com
2. Create a new project
3. Enable:
   - **Authentication** → Email/Password
   - **Firestore Database** → Start in production mode
   - **Cloud Functions** (requires Blaze plan)
   - **Cloud Messaging** (FCM)
   - **Hosting**

### 2. Get Firebase Config
In Firebase Console → Project Settings → Your apps → Add Web App.
Copy the config object and paste into `src/firebase/config.js`.

### 3. Get Google Maps API Key
1. Go to https://console.cloud.google.com
2. Enable: Maps JavaScript API, Directions API
3. Create an API key
4. Paste it into `src/pages/RoutePlanner.jsx` → `GOOGLE_MAPS_API_KEY`

### 4. Get FCM VAPID Key
Firebase Console → Project Settings → Cloud Messaging → Web Push certificates → Generate key pair.
Paste into `src/firebase/messaging.js` → `VAPID_KEY`.

### 5. Install & Run
```bash
npm install
npm run dev
```

### 6. Deploy Functions
```bash
cd functions
npm install
npm run build
cd ..
firebase deploy --only functions
```

### 7. Deploy Full App
```bash
npm run build
firebase deploy
```

---

## 🗄️ Firestore Collections

### `users/{userId}`
| Field | Type | Description |
|-------|------|-------------|
| name | string | User display name |
| email | string | User email |
| location | string | User's city |
| role | string | `user` or `admin` |
| fcmToken | string | Push notification token |

### `disasterAlerts/{alertId}`
| Field | Type | Description |
|-------|------|-------------|
| disasterType | string | Flood / Fire / Earthquake / Cyclone |
| severity | string | Low / Medium / High |
| location | string | City name |
| latitude | number | GPS latitude |
| longitude | number | GPS longitude |
| timestamp | timestamp | When created |
| status | string | Active / Resolved |

### `shelters/{shelterId}`
| Field | Type | Description |
|-------|------|-------------|
| name | string | Shelter name |
| latitude | number | GPS latitude |
| longitude | number | GPS longitude |
| capacity | number | Max occupancy |
| contactNumber | string | Phone number |

---

## 👑 Creating an Admin User
After registering normally, open Firestore in the Firebase Console,
find the user document in `users/` and manually change `role` from `"user"` to `"admin"`.

---

## 🔔 FCM Flow
1. User logs in → app requests notification permission
2. FCM token saved to `users/{uid}.fcmToken`
3. Admin creates a new alert in Firestore
4. Cloud Function (`onAlertCreated`) triggers
5. Function collects all FCM tokens and sends multicast notification
6. Users receive push notification in browser/device

---

## 📦 Tech Stack
- React 18 + Vite
- Firebase SDK v9 (modular)
- Firebase Auth, Firestore, Functions, Hosting, FCM
- Google Maps JavaScript API
- React Router v6
