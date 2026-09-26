# Johari Workshop

A live Johari Window exercise: everyone opens the same link, enters their name/team/town,
selects the classic 55 adjectives that describe themselves, then rates as many colleagues
as they like. A facilitator view lets you browse everyone who's joined and compare any
selection of people's Johari Windows side by side.

This is a static site (plain HTML/CSS/JS) that syncs data between everyone's devices
using [Firebase Firestore](https://firebase.google.com/docs/firestore) (it's on Firebase's
free "Spark" plan, no credit card required for this scale of use).

## One-time setup (you, the facilitator)

1. Go to <https://console.firebase.google.com/>, sign in with a Google account, and click
   **Add project**. Name it anything (e.g. `johari-workshop`). You can skip Google
   Analytics.
2. In the left sidebar, open **Build → Firestore Database**, click **Create database**,
   choose a region close to your team, and start in **test mode** for now (we'll tighten
   the rules in step 5).
3. In the left sidebar, click the gear icon → **Project settings**, scroll to **Your apps**,
   click the `</>` (web) icon, give the app any nickname, and skip Firebase Hosting. You'll
   land on a config snippet that looks like:
   ```js
   const firebaseConfig = {
     apiKey: "AIza...",
     authDomain: "johari-workshop-xxxxx.firebaseapp.com",
     projectId: "johari-workshop-xxxxx",
     storageBucket: "johari-workshop-xxxxx.appspot.com",
     messagingSenderId: "...",
     appId: "..."
   };
   ```
4. Open [`firebase-config.js`](firebase-config.js) in this repository and replace the
   placeholder values with your own from step 3. Commit and push the change (or edit the
   file directly on GitHub and commit there) — GitHub Pages will pick it up automatically
   within a minute or two.
5. Back in the Firebase console, go to **Firestore Database → Rules** and replace the
   default rules with:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /participants/{id} {
         allow read, write: if true;
       }
       match /ratings/{id} {
         allow read, write: if true;
       }
     }
   }
   ```
   Click **Publish**. This keeps the database open to anyone with the link (there's no
   login step for participants), scoped to just the two collections this app uses. Since
   this is a short-lived internal exercise, that's a reasonable trade-off — just don't
   reuse the same Firebase project for anything sensitive, and consider deleting the
   project (or clearing its data via the in-app **Reset workshop** button) once the
   workshop is done.

That's it — reload the page and it should show "Synced live across devices" in the header
instead of "Setup needed".

## Running a workshop

1. Share the page's URL (see below) with everyone taking part.
2. Each person opens it, enters their Name / Team / Base Town, and picks the words that
   describe themselves.
3. They then see everyone else who's joined and can rate as many colleagues as they like
   (the app suggests 3–5).
4. You open the same link and click **Results (facilitator)** in the top right (or add
   `#results` to the URL) to see the roster and compare anyone's Johari Window.
5. When you're done, use **Reset workshop** on the Results page to clear everything for
   next time — it deletes all participants and ratings from Firestore.

## Files

- `index.html` — the whole app (UI + logic).
- `firebase-config.js` — your Firebase project's web config. Safe to be public; it's not a
  secret, access is controlled by the Firestore rules above instead.

## Local testing

Just open `index.html` in a browser, or serve the folder with any static server, e.g.:

```bash
npx serve .
```
