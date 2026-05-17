# Privacy and Validation

## Privacy Boundary

For a new local-first Safari extension, prefer this baseline:

- Settings stay in extension local storage.
- Page behavior runs locally in Safari.
- No browsing history, page content, account data, form input, analytics, ads, or tracking are collected unless the product explicitly requires it.

For existing products, preserve current account, backend, analytics, sync, billing, and privacy behavior unless the user asks to change them. Review whether the requested work changes data collection, permissions, entitlements, or App Store privacy answers.

Both app and extension targets should include `PrivacyInfo.xcprivacy`. The manifest must match actual behavior.

## Permissions and Entitlements

Common permission rationale:

- `storage`: save extension settings locally.
- `activeTab`: use active tab context after user interaction.
- `scripting`: inject scripts into already-open tabs when needed.
- Host permissions: read or modify pages within the declared scope.

Entitlement guidance:

- Host app: keep outgoing network and file access off unless the app needs them.
- Existing host app: preserve current entitlements unless the requested work requires review.
- Extension: network access may be justified for current-page resources or product-specific remote APIs, but document why.

Any new permission or entitlement must be explainable by the feature it enables.

## Minimal Validation

After changing extension scripts, adapt paths and run:

```bash
node --check '<extension-resources>/content.js'
node --check '<extension-resources>/background.js'
node --check '<extension-resources>/popup.js'
python3 -m json.tool '<extension-resources>/manifest.json' >/tmp/safari-extension-manifest.json
```

After changing Swift, Xcode settings, signing, entitlements, or before release, adapt project and scheme names:

```bash
xcodebuild -project '<Project>.xcodeproj' -scheme '<Scheme>' -configuration Debug -derivedDataPath /tmp/<Project>DerivedData CODE_SIGNING_ALLOWED=NO build
```

## Safari Debugging Checklist

- Enable the extension in Safari Settings.
- Reload existing tabs after first install, or let the popup inject scripts when supported.
- Use Safari Web Inspector to inspect content script and popup errors.
- If popup changes do nothing, check tab messaging and the `scripting` permission.
- If page behavior is missing in embedded content, inspect every frame and verify content scripts ran where expected.
- If dynamic content is missed, check whether the page inserted new containers after initial processing.
- If the host app cannot open settings, check the extension bundle identifier.
- If settings do not persist, check extension local storage access.

## Manual Scenarios

Choose scenarios that match the feature:

- Host app extension status display.
- Host app button opens Safari Settings.
- Popup opens and shows the expected current-site context.
- Popup settings persist after closing and reopening.
- Popup changes apply without a refresh when the feature promises that behavior.
- Existing tabs behave correctly after install or update.
- iframe or embedded content works when frame coverage is required.
- Dynamic web apps work when page processing is required.
- Permission prompts and App Store privacy answers match actual behavior.
