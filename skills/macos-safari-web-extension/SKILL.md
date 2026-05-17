---
name: macos-safari-web-extension
description: Build, review, or fix macOS Safari Web Extensions. Use for SwiftUI host apps, Safari toolbar popups, Manifest V3, extension storage, content/background/popup script contracts, permissions, privacy manifests, App Store validation, and optional local page-processing features such as dark mode.
---

# macOS Safari Web Extension

Use this skill when working on a macOS Safari Web Extension, whether creating a new extension or improving an existing one.

## Workflow

1. Confirm the work is for macOS Safari. Do not expand scope to iPhone, iPad, Chrome, backend services, accounts, analytics, or billing unless the user asks.
2. Preserve the existing product architecture first. Add the smallest focused change that satisfies the request.
3. Read [project-shape.md](references/project-shape.md) when creating, restructuring, or integrating the host app and extension target.
4. Read [extension-contract.md](references/extension-contract.md) before changing `manifest.json`, content scripts, background scripts, popup files, storage keys, or messaging.
5. Read [privacy-validation.md](references/privacy-validation.md) before changing permissions, entitlements, privacy manifests, release docs, or validation commands.
6. Implement in small slices and validate the affected host app, extension scripts, manifest, and Safari behavior.

## New Extension Path

For a new Safari extension:

1. Create a macOS app with a Safari Web Extension target.
2. Keep the host app small: product identity, extension status, enablement guidance, and a button to open Safari Settings.
3. Use Manifest V3 and request only permissions that the feature needs.
4. Put daily controls in the Safari toolbar popup.
5. Use extension local storage for user settings.
6. Add content scripts, background scripts, native handlers, or page UI only when the product behavior requires them.
7. Add privacy manifests and run validation before release.

## Existing Extension Path

For an existing Safari extension:

1. Inspect the current manifest, popup, content scripts, background scripts, storage keys, permissions, entitlements, and host app behavior.
2. Reuse existing architecture, naming, UI surfaces, and storage patterns when possible.
3. Add new controls to the existing popup or settings surface instead of redesigning unrelated UI.
4. Request new permissions only when the new feature needs them.
5. Keep account, backend, sync, analytics, billing, and release behavior unchanged unless the user explicitly asks to change them.

## Implementation Priorities

- Host app: status and enablement guidance, not a default full settings center.
- Popup: compact daily controls with clear current-site context when relevant.
- Content scripts: use `document_start`, `all_frames`, or `match_about_blank` only when the feature needs early or frame-wide page behavior.
- Background scripts: keep cross-context work small, message-based, and explainable.
- Storage: prefer local extension storage for extension settings.
- Compatibility: use behavior-based fixes before domain-specific patches.
- Page UI: optional overlays or helper controls should be local, lightweight, and off by default unless the product requires otherwise.

## Validation

Run syntax checks, manifest validation, and a macOS build after relevant changes. Use the commands and manual scenarios in [privacy-validation.md](references/privacy-validation.md), adapting paths and scheme names to the target project.
