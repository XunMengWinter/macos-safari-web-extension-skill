# Extension Contract

## Script Responsibilities

Content scripts may:

- Run page-level behavior.
- Read extension settings.
- React to popup or background messages.
- Observe page changes when dynamic content matters.
- Apply behavior in frames when the feature requires frame coverage.

Background scripts or service workers may:

- Initialize default settings.
- Migrate stored data.
- Coordinate messages between popup and content scripts.
- Perform extension-context work that content scripts cannot safely do.

Popup scripts may:

- Read the active tab and current site context.
- Render user settings and actions.
- Save changes to extension local storage.
- Notify the active tab or all relevant frames to reapply behavior.
- Inject missing scripts into already-open tabs when the feature and permissions allow it.

When an existing extension already has these scripts, extend them without breaking unrelated behavior.

## Local Settings

Use the extension's existing storage API and naming style when possible. New projects can start with a small local settings object:

```json
{
  "enabled": true,
  "disabledHosts": []
}
```

Add feature-specific fields only when needed. Keep page UI settings, site-specific settings, and processing settings separate enough to migrate safely.

## Popup Reapply Contract

On a setting or command change:

1. Save the changed setting.
2. Send a message to the active tab or relevant frames.
3. If messaging fails because the tab was opened before scripts were available, inject the required scripts when permitted.
4. Retry the message or show a short actionable failure state.

Keep site-specific controls disabled when the active tab has no valid site context.

## Page Processing Features

For features that modify pages, such as dark mode, content filtering, highlighting, or helper overlays:

- Prefer local processing.
- Avoid uploading page content unless the product explicitly requires it and privacy docs are updated.
- Use `document_start` only when first-paint behavior matters.
- Use `all_frames` and `match_about_blank` when iframe or embedded content must be covered.
- Cleanly disable or remove injected styles and UI when the user turns the feature off.
- Use behavior-based compatibility fixes before domain-specific patches.

## Cross-Origin Resources

If page processing needs resources that page-context code cannot fetch because of CORS:

- Route the request through the background script only when permission and product behavior justify it.
- Accept only expected URL schemes, usually `http:` and `https:`.
- Avoid credentials unless the feature explicitly requires them and privacy review covers it.
- Degrade gracefully when a resource cannot be fetched.

## Compatibility Checklist

When a page feature fails, check:

- Content script injection timing.
- Frame coverage.
- Missing or overly narrow host permissions.
- Popup-to-content messaging.
- Dynamic app shells that render after load.
- Cross-origin resource access.
- Cleanup paths when the feature is disabled.

Prefer general fixes over one-site special cases.
