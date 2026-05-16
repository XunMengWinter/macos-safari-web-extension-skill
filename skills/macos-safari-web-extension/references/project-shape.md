# Project Shape

## From Zero in Xcode

For a new project:

1. Create a macOS App project.
2. Add a Safari Web Extension target.
3. Use SwiftUI for the host app.
4. Set the deployment target to macOS 13.0 or newer.
5. Keep App Sandbox enabled.
6. Keep the host app small: status, instructions, and open Safari Settings.
7. Sync the extension bundle identifier anywhere the host app queries or opens the extension.

## Host App

Use a small macOS SwiftUI app as the Safari extension host.

Responsibilities:

- Show product identity and extension status.
- Explain how to enable the extension.
- Open Safari Settings for the extension.
- Exit after the last window closes if that matches the app's desired lifecycle.

Avoid:

- Full settings surfaces in the host app.
- Web views for host UI.
- Backend calls.
- Account, billing, analytics, or sync flows.
- User-selected file access unless the product scope explicitly changes.

Typical files:

- `DarkSafari/DarkSafariApp.swift`: SwiftUI `@main` entry.
- `DarkSafari/ContentView.swift`: status and enablement UI.
- `DarkSafari/AppDelegate.swift`: minimal lifecycle behavior.
- `DarkSafari/PrivacyInfo.xcprivacy`: no data collection and no tracking.

Core APIs:

```swift
SFSafariExtensionManager.getStateOfSafariExtension(withIdentifier: extensionBundleIdentifier)
SFSafariApplication.showPreferencesForExtension(withIdentifier: extensionBundleIdentifier)
```

## Web Extension Bundle

Typical files:

- `DarkSafari Extension/Resources/manifest.json`
- `DarkSafari Extension/Resources/content.js`
- `DarkSafari Extension/Resources/background.js`
- `DarkSafari Extension/Resources/popup.html`
- `DarkSafari Extension/Resources/popup.css`
- `DarkSafari Extension/Resources/popup.js`
- `DarkSafari Extension/Resources/vendor/darkreader.min.js`
- `DarkSafari Extension/SafariWebExtensionHandler.swift`
- `DarkSafari Extension/PrivacyInfo.xcprivacy`

Use the native Safari handler only for bridge needs. Most page processing should stay in Web Extension scripts.

## Manifest Baseline

Use Manifest V3.

Minimum permissions:

```json
{
  "permissions": ["activeTab", "scripting", "storage"],
  "host_permissions": ["<all_urls>"]
}
```

Content script baseline:

```json
{
  "js": ["vendor/darkreader.min.js", "content.js"],
  "matches": ["<all_urls>"],
  "all_frames": true,
  "match_about_blank": true,
  "run_at": "document_start"
}
```

Action baseline:

```json
{
  "action": {
    "default_popup": "popup.html",
    "default_icon": "images/toolbar-icon.svg"
  }
}
```

Keep bundle identifiers synchronized across the Xcode target, extension Info.plist, and the host app code that queries the Safari extension state.
