# Extension Contract

## Script Responsibilities

`content.js`:

- Run at `document_start`.
- Reduce first-paint flashing when dark processing is enabled.
- Read extension local storage.
- Enable, update, or disable page processing.
- Observe macOS color-scheme changes when system mode is supported.
- Skip pages that are already dark when configured.
- Support iframes.
- Repair low-contrast text with behavior-based rules.
- Mount optional top-frame-only widgets when enabled.
When extending an existing extension, add these responsibilities without breaking its current content-script features.

`background.js`:

- Define default settings.
- Migrate older setting fields.
- Respond to runtime messages.
- Fetch same-page CSS resources for local processing when page-context CORS blocks access.

`popup.js`:

- Read the active tab and current host.
- Read and render local settings.
- Save setting changes to extension local storage.
- Notify the active tab to reapply settings.
- Inject missing content scripts into already-open tabs when message delivery fails.
When a popup already exists, add dark-mode controls to the current design instead of replacing unrelated controls.

## Minimum Dark Mode Behavior

A correct Safari dark mode extension should:

- Inject at `document_start`.
- Add a temporary dark background before storage reads finish.
- Read `mode`, `disabledHosts`, `skipDarkSites`, `brightness`, `contrast`, and `sepia`.
- Use DarkReader or equivalent local processing to enable and disable page darkening.
- Configure the runtime CSS fetch method before enabling page processing.
- Listen for system color-scheme changes when `mode` is `system`.
- Apply changes without requiring a page refresh.
- Support iframe content with `all_frames` and `match_about_blank`.
- Fall back to `scripting.executeScript()` when the current tab opened before content scripts were available.

## Local Settings

Use the extension's existing storage API and naming style when possible. Introduce new dark-mode keys only when existing keys cannot represent the feature.

A new dark-mode extension can start with:

```json
{
  "mode": "dark",
  "skipDarkSites": true,
  "brightness": 100,
  "contrast": 105,
  "sepia": 0,
  "disabledHosts": []
}
```

Field semantics:

- `mode`: `dark`, `light`, or `system`.
- `skipDarkSites`: skip processing when the page is already dark.
- `brightness`, `contrast`, `sepia`: visual tuning values.
- `disabledHosts`: hostnames where page processing is disabled.

If migrating older settings, map `enabled: false` to `mode: "light"` and `followSystem: true` to `mode: "system"`.

Optional top-frame widgets can add their own local settings, such as enabled state, disabled hosts, and saved position, but keep them separate from the core dark-mode contract.

## Page Processing Flow

1. Start at `document_start`.
2. Apply a temporary dark prepaint style only when appropriate.
3. Load and normalize settings.
4. Check host-level disablement.
5. Resolve the effective mode.
6. If `skipDarkSites` is enabled, inspect the real page background rather than the temporary prepaint background.
7. Enable or disable DarkReader-style processing.
8. Apply low-contrast text repair after dark processing.
9. Reapply when storage, popup messages, or system appearance changes require it.

## Compatibility Hardening

If important pages remain white, treat the problem as a general compatibility issue, not a one-domain patch.

Cover these cases:

- **Auth and login pages**: expect cross-origin frames. Keep `all_frames`, `match_about_blank`, and popup injection with `allFrames: true`.
- **Late app shells**: many apps render `#root`, `main`, dialogs, and fixed panels after load. Use a `MutationObserver` to repair new low-contrast or bright app-shell elements.
- **CSS fetch failures**: call the background fetch path through the runtime's fetch hook before enabling DarkReader-style processing.
- **Transparent roots**: inspect the real visible background by walking from the target element to ancestors; do not assume `body` has the effective background.
- **Skip-dark detection**: never run detection against the temporary prepaint style. If the page is still loading and the real background is unclear, prefer applying dark mode rather than incorrectly skipping.
- **Fallback styling**: keep a small removable fallback for `html`, `body`, and obvious app roots while the runtime generates styles. Remove it when light/original mode is selected.
- **Frame-local settings**: each frame should evaluate its own `location.hostname`, but popup changes should notify or inject all frames in the active tab.

Avoid:

- Domain-specific rules as the first fix.
- Disabling `all_frames` to reduce noise.
- Relying only on `body` background color.
- Fetching CSS with credentials.
- Leaving fallback dark styles active after the user chooses original mode.

## Cross-Origin CSS

When runtime page processing needs CSS that the page cannot fetch because of CORS:

- Ask `background.js` to fetch the resource.
- Accept only `http:` and `https:` URLs.
- Use `credentials: "omit"`.
- Return a data URL to the page-processing runtime.
- Degrade gracefully when a resource cannot be fetched.

## Popup Reapply Contract

On control changes:

1. Write the changed settings to extension local storage.
2. Send a message to the active tab to reapply settings.
3. If messaging fails, inject required scripts with `scripting.executeScript()`.
4. Retry the reapply message after injection.

Keep current-site controls disabled when the active tab has no valid hostname.

For existing popups:

- Keep the current popup layout and commands.
- Add the smallest dark-mode control group that fits the existing UI.
- Reuse existing save, render, and messaging helpers when they exist.
- Avoid changing unrelated popup behavior.
