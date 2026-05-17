# macOS Safari Web Extension Skill

A concise Codex Skill for building, reviewing, or fixing macOS Safari Web Extensions. It covers SwiftUI host apps, Safari toolbar popups, Manifest V3, extension storage, content/background/popup script contracts, permissions, privacy review, and App Store validation.

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

## AI Install Prompt

Copy this prompt into Codex or another coding agent:

```text
Install the Codex Skill from https://github.com/XunMengWinter/macos-safari-web-extension-skill.

Clone the repository to a temporary directory, then copy only `skills/macos-safari-web-extension` into `~/.codex/skills/macos-safari-web-extension`.

Do not install the repository root as a Skill. The installable Skill folder is `skills/macos-safari-web-extension`.

After installing, verify that `~/.codex/skills/macos-safari-web-extension/SKILL.md` exists and tell me the final installed path.
```

Chinese:

```text
请帮我安装这个 Codex Skill：https://github.com/XunMengWinter/macos-safari-web-extension-skill

请先把仓库 clone 到临时目录，然后只把 `skills/macos-safari-web-extension` 复制到 `~/.codex/skills/macos-safari-web-extension`。

不要把整个仓库根目录当作 Skill 安装。真正可安装的 Skill 文件夹是 `skills/macos-safari-web-extension`。

安装完成后，请确认 `~/.codex/skills/macos-safari-web-extension/SKILL.md` 存在，并告诉我最终安装路径。
```

## Use

English:

```text
Use $macos-safari-web-extension to create a macOS Safari Web Extension with a SwiftUI host app and toolbar popup.
```

Existing project:

```text
Use $macos-safari-web-extension to add a focused feature to this existing macOS Safari Web Extension without replacing its current architecture.
```

Chinese:

```text
使用 $macos-safari-web-extension，帮我创建一个 macOS Safari 插件项目。
```

已有项目：

```text
使用 $macos-safari-web-extension，在我现有的 macOS Safari 插件里增加一个功能，不要重写现有架构。
```

## Scope

Use this Skill for:

- macOS Safari Web Extension projects.
- New SwiftUI host apps that show extension status and open Safari Settings.
- Existing host apps that should keep their current responsibilities.
- Safari toolbar popup controls.
- Extension local storage settings.
- `manifest.json`, content scripts, background scripts, and popup scripts.
- Local page-processing features such as dark mode, highlighting, filtering, or helper overlays.
- Privacy manifests, permissions, entitlements, and validation checklists.

Do not use this Skill as the primary guide for:

- iPhone or iPad Safari extensions.
- Chrome-only extensions.
- Backend services, account systems, analytics, billing, or sync features unless they already exist in the product and are directly relevant.
