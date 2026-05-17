# Project Shape

## Existing Project First

Before changing an existing extension, inspect:

- `manifest.json`: manifest version, permissions, host permissions, content scripts, action popup, background configuration.
- Current content, background, popup, and native handler code.
- Existing storage keys and migration patterns.
- Host app responsibilities and extension bundle identifier usage.
- Current entitlements, privacy manifests, signing, and release notes.

Preserve the current architecture unless the requested feature needs a focused change.

## New Project Baseline

For a new macOS Safari extension:

1. Create a macOS app project.
2. Add a Safari Web Extension target.
3. Prefer SwiftUI for a simple host app.
4. Set a supported macOS deployment target for the intended audience.
5. Keep App Sandbox enabled.
6. Keep bundle identifiers synchronized across the Xcode target, extension Info.plist, and host app code.

## Host App

The host app usually handles:

- Product identity.
- Extension status.
- Enablement guidance.
- Opening Safari Settings for the extension.
- Minimal privacy or local-processing messaging when relevant.

Avoid turning the host app into a full settings center unless the product scope requires it.

Core Safari APIs:

```swift
SFSafariExtensionManager.getStateOfSafariExtension(withIdentifier: extensionBundleIdentifier)
SFSafariApplication.showPreferencesForExtension(withIdentifier: extensionBundleIdentifier)
```

## Web Extension Bundle

Typical resource types:

- `manifest.json`
- Content scripts, when pages need injected behavior.
- Background script or service worker, when cross-context coordination is needed.
- Popup HTML/CSS/JS, when the toolbar icon opens controls.
- Images and local assets.
- Native Safari handler, only when native bridging is needed.
- `PrivacyInfo.xcprivacy` for app and extension targets.

Most page-facing behavior should stay in Web Extension scripts. Use the native handler only for APIs that require native bridging.

## Manifest Guidance

Use Manifest V3 unless the project has a clear reason not to.

Common permissions:

- `storage`: persist extension settings.
- `activeTab`: inspect or act on the currently active tab after user interaction.
- `scripting`: inject scripts into already-open tabs when needed.

Use host permissions only for pages the extension actually needs to read or modify. If broad page processing is the product, `<all_urls>` may be appropriate, but it must be reflected in permission and privacy explanations.

Content script fields to consider when the feature needs page injection:

```json
{
  "matches": ["<all_urls>"],
  "run_at": "document_start",
  "all_frames": true,
  "match_about_blank": true
}
```

Do not include `document_start`, `all_frames`, or broad matches by default if the requested feature does not need them.
