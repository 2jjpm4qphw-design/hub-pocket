# Hub Pocket — deploy

This folder is the deploy bundle for **Hub Pocket** (the iPhone companion to The Hub).
`index.html` is generated from `_Source/Hub Pocket.html` by `build_sites.py` — never edit it here; edit the source on the home iMac.

## Files in this bundle (all go to the `hub-pocket` repo root)
- `index.html` — the app (from `_Source/Hub Pocket.html`)
- `manifest.webmanifest` — web app manifest (name, icons, standalone display)
- `sw.js` — service worker; caches the app shell so it launches with no signal
- `icon-192.png`, `icon-512.png`, `icon-maskable-512.png` — app/splash icons

All six are copied here automatically by `build_sites.py`. Their canonical copies live in `_Source/`.

## Put it on your phone (GitHub Pages)
1. Create a new GitHub repo, e.g. `hub-pocket` (public is fine — there are no secrets in the file; your token is entered on the phone and stored only there).
2. Upload **all six files** from this folder to the repo root (not just index.html — the service worker, manifest and icons must sit beside it).
3. Repo → Settings → Pages → Source: "Deploy from a branch" → branch `main`, folder `/ (root)` → Save.
4. Wait ~1 min, then open `https://<your-username>.github.io/hub-pocket/` on your iPhone in Safari.
5. Share → **Add to Home Screen**. It now installs as a PWA: launches from the home-screen icon, opens its own splash, and the shell loads even with no signal.
6. First launch: on the Mac open The Hub → tap the ⚙ → copy the **Gist ID** and **token** into Pocket's Setup screen.

## Shipping a code change
1. Edit `_Source/Hub Pocket.html` on the home iMac.
2. Redeploy (rebuilds copies + assets): `cd "_Source" && python3 build_sites.py "Executive Assistant.html"`.
   (For a Pocket-only change you can instead just re-copy the changed files from `_Source/` into this folder.)
3. Upload the changed file(s) to the `hub-pocket` repo, commit. Pages redeploys in ~1 min.

### When the shell itself changes (HTML/icons/manifest)
The service worker caches the shell. Navigations are network-first, so a new `index.html` normally shows after one online reload. To **force** every phone to drop the old cache, bump the version in `sw.js`:
`var CACHE = 'hub-pocket-v1';` → `'hub-pocket-v2'`, and upload the new `sw.js`.
If an installed PWA still won't update, pull-to-refresh, or remove and re-add the home-screen icon.

## What sync does (unchanged by the PWA)
The service worker only caches the app *shell*. It never touches gist or weather data — those always go to the network. Reading/writing The Hub's data (`life-twoweek.json`) works exactly as before: optimistic op-queue, read-modify-write, replay on flush.
