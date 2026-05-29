# Dialed Nozzle — Deploy Guide (Chromebook → GitHub Pages → Google Play)

You have everything you need in this folder. No toolchain, no Flutter, no Android SDK.
Just a browser and your GitHub account.

## What's in this folder
- `index.html` — the whole app (rebranded to Dialed Nozzle)
- `manifest.webmanifest` — makes it an installable app
- `sw.js` — service worker, makes it work offline
- `icon-192.png`, `icon-512.png`, `icon-maskable-512.png` — app icons
- `apple-touch-icon.png`, `favicon-32.png` — extra icons
- `icon.svg`, `icon-maskable.svg` — editable icon source

Keep ALL of these together in the same folder. They reference each other.

---

## STEP 1 — Put it on GitHub Pages (free hosting)

1. Go to github.com and create a new **public** repository named `dialed-nozzle`.
2. On the repo page, click **Add file → Upload files**.
3. Drag in EVERY file from this folder (all the .html, .png, .svg, .webmanifest, .js).
4. Click **Commit changes**.
5. Go to **Settings → Pages** (left sidebar).
6. Under "Build and deployment", set **Source: Deploy from a branch**, branch **main**, folder **/ (root)**. Save.
7. Wait ~1 minute. The page will show your live URL, like:
   `https://YOURNAME.github.io/dialed-nozzle/`
8. Open that URL on your phone. You should see the app. In Chrome's menu you'll
   see **"Install app"** or **"Add to Home screen"** — that confirms the PWA works.

✅ At this point you have a real, installable, offline-capable web app, live and free.

---

## STEP 2 — Turn it into a Google Play package (PWABuilder)

PWABuilder is a free Microsoft tool that wraps your PWA into a Play-ready Android
package. It runs in the browser — perfect for a Chromebook.

1. Go to **pwabuilder.com**.
2. Paste your GitHub Pages URL and click **Start**.
3. It scores your PWA. The manifest + service worker we built should pass the key
   checks. Fix anything it flags (usually nothing, given what's included).
4. Click **Package for stores → Android**.
5. Choose **"Google Play"** package type (this produces a signed `.aab` — an Android
   App Bundle, which is what Play requires).
6. PWABuilder will ask for a **package ID** — use something like
   `com.yourname.dialednozzle` (lowercase, no spaces, can't change later).
7. Download the zip. It contains:
   - the `.aab` file (this is what you upload to Play)
   - a **signing key** + instructions — KEEP THIS SAFE. You need the same key to push
     updates forever. Back it up somewhere you won't lose it.
   - `assetlinks.json` instructions (see Step 3).

---

## STEP 3 — Digital Asset Links (removes the browser bar)

For the app to open full-screen (no URL bar) instead of looking like a website,
Android needs to verify you own the site:

1. PWABuilder gives you an `assetlinks.json` file.
2. In your GitHub repo, create a folder named `.well-known` and put `assetlinks.json`
   inside it, so it's reachable at:
   `https://YOURNAME.github.io/dialed-nozzle/.well-known/assetlinks.json`
3. Commit. That's it — the app will now launch chrome-less.

> Note: GitHub Pages serves `.well-known` fine. If you ever move to a custom domain
> (dialednozzle.app), you redo this there.

---

## STEP 4 — Google Play Console

1. In Play Console (your $25 account), click **Create app**.
2. App name: **Dialed Nozzle** (full listing title can be
   "Dialed Nozzle — 3D Print & Pen Settings").
3. Upload the `.aab` to a **Closed testing** track first (NOT production yet).
4. Fill the listing (see `STORE_LISTING.md` — I'll draft this next):
   - Short description, full description
   - Screenshots (take them from your phone running the app)
   - Feature graphic, app icon (use `icon-512.png`)
   - Privacy policy URL (required — even a simple one; I can write the text)
   - Content rating questionnaire, data safety form (this app collects nothing —
     easy answers)
5. Add your ~12 testers' emails to the closed testing track.

---

## STEP 5 — The 12-tester / 14-day requirement

New personal developer accounts must run **closed testing with at least 12 testers
for 14 days** before applying for production. So:
- Recruit testers NOW (see `TESTER_RECRUITMENT.md`).
- They join via an opt-in link Play gives you, install from Play, and just need to
  have it installed across the period.
- After 14 days with 12+ testers, Play unlocks the **"Apply for production"** button.

---

## Updating the app later
1. Change `index.html` (or any file) in your GitHub repo.
2. Bump the cache version in `sw.js` (change `dialed-nozzle-v1` to `-v2`) so users
   get the update.
3. For the *web* app, that's it — it updates itself.
4. For the *Play* app, only re-package via PWABuilder if you changed the manifest or
   icons; content changes inside index.html flow through automatically because the
   wrapper loads your live URL. (This is a big advantage of the TWA approach.)

---

## If PWABuilder's Android score complains
Most common fixes, all already handled in what I built, but if flagged:
- "No service worker" → make sure `sw.js` uploaded and the URL has `/dialed-nozzle/`
  in the path correctly.
- "Manifest not found" → confirm `manifest.webmanifest` is in the repo root.
- "Icon too small / no maskable" → we include 192, 512, and a maskable 512; re-run.
