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

## Local Settings

Use extension local storage. A dark-mode extension can start with:

```json
{
  "mode": "dark",
  "skipDarkSites": true,
  "brightness": 100,
  "contrast": 105,
  "sepia": 0,
  "disabledHosts": [],
  "petEnabled": false,
  "petDisabledHosts": [],
  "petPosition": null
}
```

Field semantics:

- `mode`: `dark`, `light`, or `system`.
- `skipDarkSites`: skip processing when the page is already dark.
- `brightness`, `contrast`, `sepia`: visual tuning values.
- `disabledHosts`: hostnames where page processing is disabled.
- `petEnabled`, `petDisabledHosts`, `petPosition`: optional local-only top-frame widget settings.

If migrating older settings, map `enabled: false` to `mode: "light"` and `followSystem: true` to `mode: "system"`.

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
