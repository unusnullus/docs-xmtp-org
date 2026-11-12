---
description: "Protect your users, your bot token and your server when you build a Mini App."
---

# Mini App security checklist

A Mini App is a public web page that talks to your server. Anyone can load its URL in a browser and send any request to your API. Use this checklist before you publish.

## Identity and launch data <a href="#identity-and-launch-data" id="identity-and-launch-data"></a>

- Your server [validates `initData`](validate-launch-data.md) before it trusts any field: signature, `bot_id` and `auth_date`.
- You use a maximum age of one hour or less, and call `refreshInitData()` in long sessions instead of raising it.
- You track each `launch_id` with the newest `auth_date` you accepted, and reject older or repeated launch data.
- You check `launch_kind` against the entry points each operation expects.
- You never use `initDataUnsafe`, `aerxStartParam` or other page-supplied values to authorize anything.
- You send `initData` in a POST body over HTTPS, never in a URL, and you do not log it.

## Bot token <a href="#bot-token" id="bot-token"></a>

- The bot token exists only on your server, in a secret manager or environment variable.
- Your page, its JavaScript bundle and its source maps contain no token.
- Your Mini App server gets the new token when you rotate it. See [Security](../security.md#rotate-a-token).

## Data from the page <a href="#data-from-the-page" id="data-from-the-page"></a>

- Your bot validates `mini_app_data` service messages as untrusted input: size, format and allowed values.
- Your server validates every request body, even after the session is verified.
- You check that the verified user may perform the action, for example that the order they open is theirs.
- You answer a `query_id` only once, with an idempotency key, and only after validating the launch data that contained it.

## Origins and the page <a href="#origins-and-the-page" id="origins-and-the-page"></a>

- Every origin registered for your bot is one you control. Remove origins you no longer use.
- You keep origin protection on unless a page outside your origins needs native buttons or the theme. Pages outside your origins never get launch data.
- You serve your page over HTTPS, and you load the bridge from `https://www.arx.pro`, pinned with `integrity` if you want an exact version.
- Your page sends a Content Security Policy whose `script-src` allows only your origin and `https://www.arx.pro`.
- You do not embed third-party scripts that could read `initData` from the page.
- You escape user-controlled text before you insert it into the page.

## Storage <a href="#storage" id="storage"></a>

- `CloudStorage` and `DeviceStorage` hold no secrets. Treat values you read back as user input.
- You use `SecureStorage` or `BiometricManager.updateBiometricToken` for small secrets on the device, and check `supports()` first.
- You do not store `initData` in any storage.

## User experience and trust <a href="#user-experience-and-trust" id="user-experience-and-trust"></a>

- Your bot has a privacy policy. Set it with `/setprivacypolicy` in NexoCreator.
- You call `openLink`, `shareMessage`, `requestChat`, `downloadFile` and similar methods only from a user tap, and you explain what will happen before the tap.
- You request write access, contact sharing, location and biometrics only when the user needs that feature, and you handle a refusal.
- Payment prompts follow `paymentPolicy`, and you deliver orders only from the payment updates your bot receives.
