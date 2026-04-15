# App Store Submission Status — v1.0.0

**Last Updated:** 2026-04-15
**Prepared by:** Claude Code (today's mascot + screenshot blocker fix)

---

## ✅ Completed

### App Icon + Splash + Adaptive (re-rendered 2026-04-15)
The previous icon was the octopus mascot. As of 2026-04-04 the octopus is exclusive to Eight.ly and HomelabARR uses its own pirate-themed mascot. All app icon assets now use the new mascot:

- `assets/icon.png` (1024×1024 RGB, no alpha — Apple-compliant) — 451 KB
- `assets/splash-icon.png` (1024×1024 with alpha for Expo splash) — 665 KB
- `assets/android-icon-foreground.png` (1024×1024 with alpha, mascot at 66% safe zone) — 352 KB
- `assets/android-icon-background.png` (solid white, 1024×1024) — 4.5 KB
- `assets/favicon.png` (48×48) — 4.6 KB

Backups of the old octopus icons are at `assets/.bak-2026-04-15-octopus/`.

### Screenshots (re-captured 2026-04-15)
The previous "8 screenshots" in `appstore-assets/` were 8 byte-identical copies of one image (verified: single SHA across all 8). Apple would have rejected the submission instantly. Re-captured 12 distinct screenshots from the actual app (CE WebView rendering) at exact App Store dimensions:

**6.7" iPhone (1290×2796 — iPhone 16 Pro Max):**
- `appstore-assets/67-1-login.png` — Sign-in modal
- `appstore-assets/67-2-catalog.png` — All Apps catalog
- `appstore-assets/67-3-search.png` — Search results ("plex")
- `appstore-assets/67-4-category.png` — Media & Entertainment category
- `appstore-assets/67-5-deploy.png` — Deploy modal (app detail)
- `appstore-assets/67-6-deployed.png` — Deployed Apps tab

**6.1" iPhone (1179×2556 — iPhone 16):**
- `appstore-assets/61-1-login.png` through `61-6-deployed.png` — same 6 views

Old duplicate `simulator_screen*.png` files archived at `appstore-assets/.bak-2026-04-15-duplicates/`.

### App Metadata (Ready in APP-STORE-LISTING.md)
- ✅ Name: HomelabARR Mobile
- ✅ Subtitle: Manage Your Homelab Anywhere
- ✅ Description: Full 4000-char description written
- ✅ Keywords: homelab,docker,self-hosted,plex,sonarr,radarr,containers,server,monitoring,deploy
- ✅ Price: $4.99
- ✅ Category: Utilities (Primary), Productivity (Secondary)
- ✅ Support URL: https://homelabarr.com
- ✅ Privacy Policy: https://homelabarr.com/privacy
- ✅ Age Rating: 4+
- ✅ App Review Notes: Includes demo credentials

### App Binary
- ✅ iOS build completed (EAS build #1)
- ✅ Uploaded to TestFlight
- ✅ TestFlight testing active

> **Heads up:** the TestFlight build was made when the icon was still the octopus. After uploading the icon swap, you'll need to run `eas build --platform ios` to produce a new TestFlight build with the new mascot before the App Store version is correct. Same for Android (`eas build --platform android`). Without a rebuild, the binary will still ship with the octopus icon and the App Store metadata won't match.

---

## Submission steps (Michael, manual)

App Store Connect requires interactive auth, can't be automated.

### iOS

1. `cd ~/.openclaw/workspace/products/homelabarr-mobile`
2. `eas build --platform ios --profile production` — produces a fresh build with the new icon
3. `eas submit --platform ios` — uploads to App Store Connect, OR upload IPA manually via Transporter
4. Open https://appstoreconnect.apple.com/apps/6761244772/distribution/ios/version/inflight
5. Upload the 6 `67-*.png` screenshots to the "6.7" iPhone" section
6. Upload the 6 `61-*.png` screenshots to the "6.1" iPhone" section
7. Copy/paste metadata from `APP-STORE-LISTING.md`
8. Submit for review

### Android (Play Store)

1. `eas build --platform android --profile production` — fresh build with the new icon + adaptive icon
2. `eas submit --platform android` — or upload AAB manually via Play Console
3. Use the same screenshots (Play Console accepts the same dimensions)
4. Submit for review

---

## TestFlight Status

- **Build #1:** Live on TestFlight (with old octopus icon — see heads-up above)
- **Testers:** Leslie + others as added
- **Access:** Via email invite with API key `hlr_5426a55c...`

---

## Notes

- Mobile app is an Expo/React Native WebView around CE. Screens render whatever ce-demo (or the user's own CE install) is showing.
- The app connects to `https://ce-demo.homelabarr.com` (admin/admin) by default. Production users enter their own server URL on first launch.
- Dark mode follows system preference automatically.
