---
name: macos-safari-web-extension
description: Build, replicate, review, or fix macOS Safari Web Extensions with SwiftUI host apps, Safari toolbar popups, Manifest V3, extension local storage, content/background/popup script contracts, DarkReader-style local page processing, privacy boundaries, permissions, entitlements, and App Store validation.
---

# macOS Safari Web Extension

Use this skill when working on a macOS-only Safari Web Extension that has a SwiftUI host app and a Web Extension bundle.

## Workflow

1. Confirm the project is macOS Safari-only. Do not add iPhone/iPad, backend, account, cloud sync, analytics, ads, subscriptions, or remote rules unless explicitly requested.
2. Read [project-shape.md](references/project-shape.md) before creating or restructuring the app and extension.
3. Read [extension-contract.md](references/extension-contract.md) before changing `manifest.json`, `content.js`, `background.js`, `popup.html`, `popup.css`, or `popup.js`.
4. Read [privacy-validation.md](references/privacy-validation.md) before changing permissions, entitlements, privacy manifests, release docs, or validation commands.
5. Keep changes local-first: page processing runs in Safari, settings stay in extension local storage, and the host app only guides extension enablement.

## Build Path

For a new Safari dark mode extension:

1. Create a macOS App project with a Safari Web Extension target.
2. Keep the host app minimal: extension status, enablement text, and an open Safari Settings button.
3. Configure Manifest V3 with `storage`, `activeTab`, `scripting`, and `<all_urls>`.
4. Load `vendor/darkreader.min.js` before `content.js`.
5. Implement `background.js` for default settings, migrations, messages, and CSS fetching.
6. Implement `content.js` for prepaint, storage reads, DarkReader enable/disable, system mode, iframe support, and low-contrast repair.
7. Implement popup controls for mode, current site, brightness, contrast, sepia, and optional local-only page widgets.
8. Add privacy manifests and run validation.

## Implementation Priorities

- Prefer a small SwiftUI host app that shows extension status and opens Safari Settings.
- Keep daily controls in the Safari toolbar popup.
- Inject content scripts at `document_start` and support `all_frames` plus `match_about_blank`.
- Use local rules and behavior-based fixes before domain-specific patches.
- If using DarkReader or similar runtime processing, keep CSS/resource handling inside the extension boundary.
- Treat optional page widgets, such as a top-frame pet or helper control, as local-only features that are disabled by default.

## Validation

Run JavaScript syntax checks, manifest JSON validation, and a macOS build after relevant changes. Use the commands and manual scenarios in [privacy-validation.md](references/privacy-validation.md).
