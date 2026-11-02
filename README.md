# HomelabARR Mobile Companion

> Manage your HomelabARR CE instance from your phone or tablet.

[![Download on the App Store](https://developer.apple.com/assets/elements/badges/download-on-the-app-store.svg)](https://apps.apple.com/us/app/homelabarr-mobile/id6761244772)

Expo SDK 58 WebView wrapper around the existing HomelabARR CE dashboard (which is already mobile-responsive).

## See it work

[![HomelabARR Mobile connecting to the demo server, signing in, searching, opening a deploy dialog and listing deployed apps](docs/media/demo.gif)](docs/media/demo.mp4)

A minute in the app against the public demo, [demo.homelabarr.com](https://demo.homelabarr.com): connect, sign in, search, deploy, see what is running. [Watch the full-quality MP4](docs/media/demo.mp4).

| Connect | Catalog | Search | Deploy | Running apps |
|---|---|---|---|---|
| <img src="fastlane/screenshots/en-US/iPhone69-1-setup.png" width="160" alt="Setup screen with server URL and API key fields"> | <img src="fastlane/screenshots/en-US/iPhone69-2-catalog.png" width="160" alt="All 117 apps in the catalog"> | <img src="fastlane/screenshots/en-US/iPhone69-3-search.png" width="160" alt="Search results for plex"> | <img src="fastlane/screenshots/en-US/iPhone69-5-deploy.png" width="160" alt="Deploy dialog for Plex"> | <img src="fastlane/screenshots/en-US/iPhone69-6-deployed.png" width="160" alt="Deployed apps with their status"> |

On iPad the dashboard uses the full width:

<img src="fastlane/screenshots/en-US/iPadPro13-2-catalog.png" width="560" alt="The catalog on an iPad Pro 13-inch">

## Current version

1.1.0. What changed is in `fastlane/metadata/en-US/release_notes.txt`.

## Download

- **iOS**: [App Store](https://apps.apple.com/us/app/homelabarr-mobile/id6761244772) (live)
- **Android**: not released yet

## Features

- **First-launch setup** — enter server URL + optional API key
- **Remembers your server** — across launches; the API key is kept in the iOS keychain
- **Stays on your server** — only pages on your server open in the app, everything else opens in Safari
- **Dark/light mode** — follows system theme, syncs with CE dashboard
- **Pull-to-refresh** — swipe down to reload
- **Back navigation** — swipe gesture + header back button
- **External links** — opens in system browser
- **Connection error handling** — retry/change server on failure
- **Haptic feedback** — on connect/disconnect actions
- **NSAllowsLocalNetworking** — works with local IPs (192.168.x.x, Tailscale, etc.)

## Setup

1. Download from the [App Store](https://apps.apple.com/us/app/homelabarr-mobile/id6761244772)
2. Enter your HomelabARR CE server URL (e.g. `http://192.168.1.195:8084`)
3. Optionally enter your API key for authenticated access
4. Tap Connect

## Supported Server Connections

- **Local IP**: `http://192.168.1.195:8084`
- **Tailscale**: `https://homelabarr.tailnet-name.ts.net`
- **Public domain**: `https://homelabarr.example.com`
- **Cloudflare Tunnel**: Any HTTPS URL

## Development

```bash
cd homelabarr-mobile
npm install
npx expo start
```

Scan the QR code with Expo Go on your phone.

`npm install` also applies `patches/expo-modules-jsi+58.0.21.patch`. Without it the iOS build fails on Xcode 26.2 and newer with "sending 'slot' risks causing data races" ([expo/expo#52754](https://github.com/expo/expo/issues/52754)). Delete the patch, and `patch-package` with it, once Expo ships a fix.

## Build

App Store builds are made on the Development iMac, never on the Mac mini. 1.1.0 was built with Xcode and an App Store Connect API key, no Expo account needed:

```bash
npx expo prebuild --platform ios --clean
xcodebuild -workspace ios/HomelabARRMobile.xcworkspace -scheme HomelabARRMobile \
  -configuration Release -destination generic/platform=iOS -archivePath build/HomelabARR.xcarchive \
  DEVELOPMENT_TEAM=3FAA5XG574 CODE_SIGN_STYLE=Automatic -allowProvisioningUpdates \
  -authenticationKeyPath ~/.appstoreconnect/private_keys/AuthKey_<id>.p8 \
  -authenticationKeyID <id> -authenticationKeyIssuerID <issuer> archive
xcodebuild -exportArchive -archivePath build/HomelabARR.xcarchive -exportPath build/export \
  -exportOptionsPlist ExportOptions.plist -allowProvisioningUpdates <same three key flags>
```

`ExportOptions.plist` sets `method` to `app-store-connect` and `destination` to `upload`, so the export goes straight to App Store Connect. Over ssh the login keychain is locked and codesign fails; run both commands in the logged-in session with `sudo launchctl asuser <uid> sudo -u <user> <script>`.

EAS still works once someone is signed in (`eas login`, then `eas build --platform ios`).

## Store screenshots

The App Store screenshots in `fastlane/screenshots/en-US` are the real app connected to the public demo (`https://demo.homelabarr.com`, `admin` / `admin`). Retake them on the iMac with Maestro, after a Release simulator build:

```bash
maestro --device <iPhone 17 Pro Max udid> test -e OUT=$PWD/out/iphone fastlane/maestro/iphone.yaml
maestro --device <iPad Pro 13-inch udid> test -e OUT=$PWD/out/ipad fastlane/maestro/ipad.yaml
```

Set the status bar first: `xcrun simctl status_bar <udid> override --time 9:41 --batteryState charged --batteryLevel 100`.

## Releases in Jira

Whoever cuts a release records it in Jira with `keen release sync --repo-path . --project HLMOB --tag <tag> --head origin/main --tag-pattern '^v?\d+\.\d+\.\d+(-beta)?$'`.

## Architecture

Single-file app (`App.tsx`). No router needed — the WebView handles all navigation internally.

The CE dashboard is already fully responsive, so this is intentionally thin: URL setup, WebView, done.

## Related

| Repo | Description |
|------|-------------|
| [imogenlabs/homelabarr-ce](https://github.com/imogenlabs/homelabarr-ce) | HomelabARR CE, the self-hosted dashboard this app opens |
| [eight.ly](https://eight.ly) | Eight.ly OS, the full homelab operating system from the same team |

---

Built by [Imogen Labs](https://imogenlabs.ai)
