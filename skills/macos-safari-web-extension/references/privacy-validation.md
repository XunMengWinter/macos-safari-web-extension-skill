# Privacy and Validation

## Privacy Boundary

For new dark-mode extensions, prefer a local-first privacy baseline:

- Dark-mode processing runs in Safari.
- Dark-mode settings stay in extension storage.
- The dark-mode feature does not need browsing history, page content upload, account data, search text, form input, analytics, ads, or tracking.

For existing products, preserve current account, backend, analytics, sync, and billing behavior. Review whether the dark-mode work changes data collection, permissions, entitlements, or App Store privacy answers. Do not remove or redesign existing product systems unless the user asks.

Both app and extension targets should include `PrivacyInfo.xcprivacy`. For a purely local dark-mode tool, the expected App Store privacy baseline is `Data Not Collected`.

## Permissions and Entitlements

Permission rationale:

- `activeTab`: inspect the active tab context for popup state and current-site controls.
- `scripting`: inject content scripts into already-open tabs when needed.
- `storage`: persist extension settings locally.
- `<all_urls>`: process user-visited pages and fetch current-page CSS resources for local processing.

Entitlement baseline:

- New local-first host app: usually no outgoing network and no user-selected file access.
- Existing host app: preserve current entitlements unless dark-mode work requires a reviewed change.
- Extension: outgoing network may be needed for fetching current-page CSS resources used by local page processing.

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
- If a login page stays white, inspect every frame and verify content scripts ran in the login iframe.
- If an app shell stays white, check whether the white container was inserted after DarkReader started.
- If a page is incorrectly skipped as already dark, test with `skipDarkSites` disabled and review the real background detection.
- If the host app cannot open settings, check the extension bundle identifier.
- If settings do not persist, check extension local storage access.

## Manual Safari Scenarios

Test:

- A bright page on first load.
- A page that is already dark.
- iframe login or embedded content.
- A complex login page with cross-origin authentication frames, for example App Store Connect login.
- Pages with cross-origin CSS.
- A JavaScript app shell that renders content after initial load.
- Pages with hardcoded dark text on dark backgrounds.
- Per-site disable and restore.
- Popup setting changes without refresh.
- Optional top-frame widget disabled by default, enabled, moved, hidden, and restored.
- Host app extension status display.
- Host app button opens Safari Settings.
