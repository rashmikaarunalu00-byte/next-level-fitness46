# Next Level Fitness

Workout, nutrition and progress tracker. Local-first (data stays on your device) with optional Supabase cloud sync.
The web app lives in the repo root and is wrapped by an Android WebView app (`com.nextlevelfitness.mynextlevelfitness`).

## What is in this repo

| Path | What |
|---|---|
| repo root | The whole app (HTML/CSS/JS). This is exactly what goes into the APK's `assets` folder. |
| `vendor/` | Chart.js 4.4.1, jsPDF 2.5.1, canvas-confetti 1.6.0, tweetnacl – **bundled locally, no CDN needed**. |
| `tailwind.css` | Pre-built Tailwind 3.4.17 CSS (generated – see below). |
| `tailwind.config.js`, `tailwind.input.css` | Inputs used to regenerate `tailwind.css`. |

## Run in a browser

```bash
python -m http.server 8000     # then open http://localhost:8000
```

## Rebuild the Tailwind CSS (only needed if you add new utility classes)

Use the standalone Tailwind CLI v3.4.17 (no Node needed):

```bash
tailwindcss -c tailwind.config.js -i tailwind.input.css -o tailwind.css --minify
```

## Update the Android app

1. Open your Android Studio project.
2. Delete everything in `app/src/main/assets/`, then copy everything in this repo (except README/.gitignore/tailwind config) there.
3. In `AndroidManifest.xml`, add to `MainActivity`:
   `android:configChanges="orientation|screenSize|smallestScreenSize|screenLayout|keyboard|keyboardHidden|uiMode|density"`
   and `android:windowSoftInputMode="adjustResize"`.
4. Run / build the APK. (Keeping your own signing key means the new APK installs over the old one without losing data.)

Release APKs are attached on the GitHub **Releases** page – they are not committed to git.

## Fixes in this version

* **Volume & Strength Trend chart was blank** and cards looked broken (`Chest3 sets`, wrapped buttons).
  Cause: Chart.js and Tailwind were loaded from CDNs, which the Android WebView could not reach. They are now bundled in the app and work fully offline.
* **Auto-fit to every screen**: fluid chart height (`clamp`), safe-area insets, small-phone / landscape / tablet rules, no horizontal scroll, buttons never wrap mid-word.
* **Rotation / keyboard**: the Android activity handles configuration changes itself, so rotating the phone resizes the UI instead of reloading it.
* Service-worker cache bumped to `v16` and now caches the bundled libraries.

## Notes

* `html2canvas` (share-image export) and `supabase-js` (cloud sync) are still loaded from a CDN when online; the app works without them (share-export and sync simply wait for a connection).
* `next-level-fitness.html` is an older single-file build kept for reference; the app uses `index.html`.
