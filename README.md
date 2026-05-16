# macOS Safari Web Extension Skill

A concise Codex Skill for building, replicating, reviewing, or fixing macOS Safari Web Extensions with SwiftUI host apps, Safari toolbar popups, local extension storage, Manifest V3, DarkReader-style page processing, privacy boundaries, and App Store validation.

Only the folder under `skills/` is the installable Skill:

```txt
skills/macos-safari-web-extension/
```

## Install

From this repository root:

```bash
mkdir -p ~/.codex/skills
cp -R skills/macos-safari-web-extension ~/.codex/skills/
```

Or from this package inside another repository:

```bash
mkdir -p ~/.codex/skills
cp -R docs/copy/macos-safari-web-extension-skill/skills/macos-safari-web-extension ~/.codex/skills/
```

## Use

English:

```text
Use $macos-safari-web-extension to create a macOS Safari Web Extension with a SwiftUI host app and toolbar popup.
```

Chinese:

```text
使用 $macos-safari-web-extension，帮我复刻一个 macOS Safari 深色模式扩展项目。
```

## Scope

Use this Skill for:

- macOS Safari Web Extension projects.
- SwiftUI host apps that show extension status and open Safari Settings.
- Safari toolbar popup controls.
- Extension local storage settings.
- `content.js`, `background.js`, and `popup.js` contracts.
- Local DarkReader-style page processing.
- Privacy manifests, permissions, entitlements, and validation checklists.

Do not use this Skill as the primary guide for:

- iPhone or iPad Safari extensions.
- Chrome-only extensions.
- Backend services.
- Account systems.
- Cloud sync.
- Analytics, ads, tracking, subscriptions, or in-app purchases.
