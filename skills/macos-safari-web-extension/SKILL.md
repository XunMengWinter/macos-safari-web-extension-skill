---
name: macos-safari-web-extension
description: Build, replicate, review, or fix macOS Safari Web Extensions, including adding robust dark mode to existing extensions. Use for Safari toolbar popups, Manifest V3, extension storage, content/background/popup script contracts, DarkReader-style page processing, compatibility hardening for complex sites, privacy review, permissions, entitlements, and App Store validation.
---

# macOS Safari Web Extension

Use this skill when working on a macOS Safari Web Extension, whether creating a new extension or adding dark mode to an existing extension.

## Workflow

1. Confirm the work is for a macOS Safari Web Extension. Preserve the existing product scope and architecture unless the user asks for a broader change.
2. Read [project-shape.md](references/project-shape.md) before creating, restructuring, or integrating with the app and extension.
3. Read [extension-contract.md](references/extension-contract.md) before changing `manifest.json`, `content.js`, `background.js`, `popup.html`, `popup.css`, or `popup.js`.
4. Read [privacy-validation.md](references/privacy-validation.md) before changing permissions, entitlements, privacy manifests, release docs, or validation commands.
5. Prefer local-first dark-mode processing. For existing products, keep current account, backend, analytics, sync, and billing behavior unchanged unless dark-mode support explicitly requires review.

## New Extension Path

For a new Safari dark mode extension:

1. Create a macOS App project with a Safari Web Extension target.
2. Prefer a small SwiftUI host app: extension status, enablement text, and an open Safari Settings button.
3. Configure Manifest V3 with `storage`, `activeTab`, `scripting`, and `<all_urls>`.
4. Load `vendor/darkreader.min.js` before `content.js`.
5. Implement `background.js` for default settings, migrations, messages, and CSS fetching.
6. Implement `content.js` for prepaint, storage reads, DarkReader enable/disable, system mode, iframe support, and low-contrast repair.
7. Implement popup controls for mode, current site, brightness, contrast, sepia, and optional local-only page widgets.
8. Add a compatibility pass for login pages, app shells, cross-origin iframes, and CSS fetch failures.
9. Add privacy manifests and run validation.

## Existing Extension Path

For an existing Safari extension:

1. Inspect the current manifest, popup, content scripts, background scripts, storage keys, permissions, entitlements, and host app behavior.
2. Add dark-mode settings using existing storage patterns when possible.
3. Add or extend content script processing without replacing unrelated extension behavior.
4. Add dark-mode controls to the existing popup instead of redesigning the full popup.
5. Request new permissions only when the dark-mode feature needs them.
6. Run compatibility tests on complex pages, especially login pages, app shells, iframes, and cross-origin CSS pages.

## Implementation Priorities

- Preserve existing architecture first; use the new-extension defaults only when starting from scratch.
- Prefer a small SwiftUI host app for new macOS Safari extension projects.
- Keep dark-mode controls in the Safari toolbar popup unless the existing extension has another established settings surface.
- Inject content scripts at `document_start` and support `all_frames` plus `match_about_blank`.
- Use local rules and behavior-based fixes before domain-specific patches.
- If using DarkReader or similar runtime processing, keep CSS/resource handling inside the extension boundary.
- Treat pages that stay white as a compatibility bug to investigate across frames, CSS fetching, skip-dark detection, and late-rendered app roots.
- Treat optional page widgets, such as a top-frame pet or helper control, as local-only features that are disabled by default.

## Validation

Run JavaScript syntax checks, manifest JSON validation, and a macOS build after relevant changes. Use the commands and manual scenarios in [privacy-validation.md](references/privacy-validation.md).
