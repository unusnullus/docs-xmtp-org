---
description: "Understand API versions, SDK versions and compatibility changes."
---

# Versioning & deprecation

Check API and SDK versions separately when you plan an upgrade. Your API base URL selects the major API version; your package dependency selects the SDK version you run.

## API versions <a href="#api-versions" id="api-versions"></a>

The current API path is `/bot-api/v1`. The [reference](api.md) describes its operations, request fields and responses. Do not send fields that the operation does not accept.

Write response handling so that additional optional fields do not break your application. An unknown update type should not crash a worker or prevent later updates from being processed. Keep the original payload when you need to inspect an unknown type, subject to your data retention policy.

## SDK versions <a href="#sdk-versions" id="sdk-versions"></a>

Pin the SDK version in your dependency files and retain the lockfile used by your deployment. Package versions and API path versions are independent; upgrading an SDK does not select a different API environment.

SDK versions use major, minor and patch numbers, with a prerelease suffix when present. Review the [changelog](changes.md) before upgrading, especially between prerelease versions. Compile your handlers and run [your bot's tests](testing.md) against the version you intend to deploy.

## Deprecation and removal <a href="#deprecation-and-removal" id="deprecation-and-removal"></a>

Use the [changelog](changes.md) and its Atom feed to track public changes. Deprecation entries identify the affected operation or field, its replacement, and any announced removal version or date. Apply only the dates and compatibility commitments stated in that notice; do not assume a fixed grace period.

When you replace a deprecated feature, update your requests, stored payloads and retry handling together. Use [Support](support.md) if a change prevents your integration from working.
