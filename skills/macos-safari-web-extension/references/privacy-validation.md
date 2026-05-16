# Privacy and Validation

## Privacy Boundary

Keep the extension local-first:

- Do not collect browsing history.
- Do not collect page content.
- Do not collect account data.
- Do not collect search text or form input.
- Do not upload settings.
- Do not add analytics, ads, tracking SDKs, accounts, cloud sync, remote config, subscriptions, or in-app purchases by default.

Both app and extension targets should include `PrivacyInfo.xcprivacy` declaring no data collection and no tracking.

For App Store privacy, the expected baseline is `Data Not Collected`.

## Permissions and Entitlements

Permission rationale:

- `activeTab`: inspect the active tab context for popup state and current-site controls.
- `scripting`: inject content scripts into already-open tabs when needed.
- `storage`: persist extension settings locally.
- `<all_urls>`: process user-visited pages and fetch current-page CSS resources for local processing.

Entitlement baseline:

- Host app: no outgoing network and no user-selected file access.
- Extension: outgoing network may be allowed only for fetching current-page CSS resources needed by local page processing.

Any new permission or entitlement must be explainable by the local extension feature it enables.

## Minimal Validation

After changing extension scripts:

```bash
node --check 'DarkSafari Extension/Resources/content.js'
node --check 'DarkSafari Extension/Resources/background.js'
node --check 'DarkSafari Extension/Resources/popup.js'
python3 -m json.tool 'DarkSafari Extension/Resources/manifest.json' >/tmp/darksafari-manifest.json
```

After changing Swift, Xcode settings, signing, entitlements, or before release:

```bash
xcodebuild -project DarkSafari.xcodeproj -scheme DarkSafari -configuration Debug -derivedDataPath /tmp/DarkSafariDerivedData CODE_SIGNING_ALLOWED=NO build
```

Adapt paths and scheme names for the target project.

## Safari Debugging Checklist

When testing locally:

- Enable the extension in Safari Settings.
- Reload existing tabs after first install, or let the popup inject content scripts.
- Use Safari Web Inspector to inspect content script errors.
- If popup changes do nothing, check tab messaging and the `scripting` permission.
- If pages stay partly bright, check cross-origin CSS fetching.
- If the host app cannot open settings, check the extension bundle identifier.
- If settings do not persist, check extension local storage access.

## Manual Safari Scenarios

Test:

- A bright page on first load.
- A page that is already dark.
- iframe login or embedded content.
- Pages with cross-origin CSS.
- Pages with hardcoded dark text on dark backgrounds.
- Per-site disable and restore.
- Popup setting changes without refresh.
- Optional top-frame widget disabled by default, enabled, moved, hidden, and restored.
- Host app extension status display.
- Host app button opens Safari Settings.
