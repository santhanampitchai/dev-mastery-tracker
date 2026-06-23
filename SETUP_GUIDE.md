# Dev Mastery Tracker — Setup & Deployment Guide

Your app is a single `index.html` file with Firebase Firestore for cloud sync.
Follow these steps exactly — it takes about 15 minutes total.

---

## Step 1 — Create a Firebase project

1. Go to **https://console.firebase.google.com**
2. Click **"Add project"**
3. Name it anything, e.g. `dev-mastery-tracker`
4. Disable Google Analytics (not needed) → **Create project**

---

## Step 2 — Enable Google Sign-In

1. In the Firebase Console sidebar, go to **Build → Authentication**
2. Click **"Get started"**
3. Under "Sign-in method", click **Google** → toggle **Enable** → add your email as support email → **Save**

---

## Step 3 — Create a Firestore database

1. In the sidebar, go to **Build → Firestore Database**
2. Click **"Create database"**
3. Choose **"Start in production mode"** → **Next**
4. Pick a location closest to you (e.g. `us-central1` for USA, `europe-west1` for Europe) → **Enable**

---

## Step 4 — Set Firestore security rules

1. In Firestore, click the **"Rules"** tab
2. Replace everything with the rules below → **Publish**

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

This means: each user can only read/write their own data. Nobody else can access yours.

---

## Step 5 — Get your Firebase config

1. In the Firebase Console, click the **gear icon ⚙️** → **Project settings**
2. Scroll down to **"Your apps"** → click **"</> Web"**
3. Register the app with any nickname (e.g. `tracker-web`) → **Register app**
4. You'll see a block like this — copy it:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "your-project.firebaseapp.com",
  projectId: "your-project",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

---

## Step 6 — Paste config into index.html

Open `index.html` in any text editor (VS Code recommended).

Find this block near the bottom of the file (around line 490):

```js
const firebaseConfig = {
  apiKey:            "YOUR_API_KEY",
  authDomain:        "YOUR_PROJECT_ID.firebaseapp.com",
  projectId:         "YOUR_PROJECT_ID",
  storageBucket:     "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId:             "YOUR_APP_ID"
};
```

Replace each `"YOUR_..."` value with the real values from Step 5. Save the file.

---

## Step 7 — Add your GitHub Pages domain to Firebase Auth

This is required so Firebase allows sign-in from your website URL.

1. Firebase Console → **Authentication → Settings → Authorized domains**
2. Click **"Add domain"**
3. Enter: `YOUR_USERNAME.github.io`  ← replace with your actual GitHub username
4. Click **Add**

---

## Step 8 — Create a GitHub repo and push

```bash
# In your terminal:
git init dev-mastery-tracker
cd dev-mastery-tracker

# Copy index.html into this folder, then:
git add index.html
git commit -m "Initial commit"

# Create a new repo on github.com first, then:
git remote add origin https://github.com/YOUR_USERNAME/dev-mastery-tracker.git
git branch -M main
git push -u origin main
```

---

## Step 9 — Enable GitHub Pages

1. Go to your repo on GitHub
2. Click **Settings** → scroll to **"Pages"** in the left sidebar
3. Under "Source": select **"Deploy from a branch"**
4. Branch: **main** / Folder: **/ (root)** → **Save**
5. Wait ~60 seconds, then visit: `https://YOUR_USERNAME.github.io/dev-mastery-tracker`

---

## Step 10 — Test it!

1. Open your GitHub Pages URL
2. Click **"Continue with Google"** — sign in with your Google account
3. Mark a topic as done — you should see "Saving…" then "Synced" in the top bar
4. Open the same URL on your phone or another browser
5. Sign in again — your progress should already be there ✓

---

## Troubleshooting

| Problem | Fix |
|---|---|
| "auth/unauthorized-domain" error | Add your GitHub Pages domain in Firebase Auth → Authorized domains (Step 7) |
| Blank page on GitHub Pages | Make sure the file is named exactly `index.html` (lowercase) |
| "Missing or insufficient permissions" | Re-check your Firestore security rules (Step 4) |
| Sign-in popup blocked | Allow popups for your site in your browser settings |
| Changes not syncing | Check the browser console for errors; verify your firebaseConfig values |

---

## Your data structure in Firestore

Each user gets one document at `users/{uid}` containing:

```json
{
  "done": { "a1": "2025-01-15", "d3": "2025-01-16" },
  "notes": {
    "a1": {
      "note": "Your notes here",
      "code": "// code snippet",
      "links": [{ "label": "Angular docs", "url": "https://angular.dev" }]
    }
  },
  "dayLogs": {
    "2025-01-15": { "summary": "Learned about components", "mood": "🔥" }
  }
}
```

---

## Free tier limits (Firebase Spark plan)

- **50,000 reads/day** — more than enough for personal use
- **20,000 writes/day** — the app batches saves with a 1.2s debounce
- **1 GB storage** — your notes and code snippets will stay well within this
- **No credit card required**

You will never hit these limits with personal daily use.
