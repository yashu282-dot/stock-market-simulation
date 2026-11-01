# Dalal Street Derby

A live, multi-team stock-trading classroom game. One instructor runs the
game; each of the 9 teams logs in on their own device and plays
independently, all seeing the same live prices and standings.

This is a single static HTML file (`index.html`) — no build step, no
server code. It syncs live state through Firebase Firestore.

## One-time setup (before the event)

### 1. Create a Firebase project and get its config

1. Go to <https://console.firebase.google.com> and create a new project
   (free tier is enough).
2. In the project, go to **Build → Firestore Database → Create database**.
   Choose any region close to you; start in **test mode** (we'll set rules
   next).
3. Go to **Firestore Database → Rules** and paste:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
         allow read, write: if true;
       }
     }
   }
   ```
   Click **Publish**. This keeps the database open — the game's own
   instructor passphrase is what actually gates who can control it. Fine
   for a single short-lived event; consider deleting the Firebase project
   after, since anyone with the project's web config could otherwise read
   or write to it indefinitely.
4. Go to **Project settings** (gear icon) → scroll to **Your apps** →
   click the **Web** icon (`</>`) to register a new web app (no Firebase
   Hosting needed, just the config). Copy the `firebaseConfig` object it
   gives you.
5. Open `index.html` in this folder, find the line starting with
   `var FIREBASE_CONFIG=` (near the top of the `<script>` block) and
   replace the `"REPLACE_ME"` placeholders with the real values from
   step 4.

### 2. Set the instructor passphrase

In the same area of `index.html`, find:
```js
var HOST_PASS="derby-falcon-42";
```
Change `"derby-falcon-42"` to whatever passphrase you want to use as the
instructor. Keep it to yourself — anyone who enters it on the "I am the
instructor" link gets full control of the game (starting/closing steps,
announcing cuts, resetting).

### 3. Deploy to Vercel

Once the two edits above are made and saved:

```sh
npx vercel          # first deploy: follow the prompts, link/create a project
npx vercel --prod    # promote to your production URL
```

Or: push this folder to a GitHub repo and import it at
<https://vercel.com/new> — Vercel auto-detects it as a static site, no
build command needed. Every `git push` after that redeploys automatically.

Share the resulting `https://<project>.vercel.app` link with your teams.

## Running the event

1. Open the deployed link yourself, click **"I am the instructor"** near
   the top, enter your passphrase.
2. Fill in the 9 team names and the stock scenarios, click **Start the
   game**.
3. Share the same link with all teams. Each team opens it, sees **"Who
   are you?"**, and picks their team name — from then on they can submit
   and lock only their own orders, on their own device.
4. You control starting/closing each trading step and announcing the
   Round 1 cut; everyone's screen updates live as you do.
