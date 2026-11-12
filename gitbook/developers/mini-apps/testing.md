---
description: "Develop and test your Mini App with a TESTBOT, a public HTTPS origin and checks for each launch kind."
---

# Test a Mini App

Test the page in a desktop browser first, then open it from a TESTBOT in ARX Messenger. The bridge only works inside ARX, so plan for both.

## Set up a TESTBOT <a href="#set-up-a-testbot" id="set-up-a-testbot"></a>

1. Create a TESTBOT with `/newtestbot` in NexoCreator. Only you and the testers you invite can open it. See [Testing](../testing.md#testbot).
2. Serve your page over HTTPS on a public host name. A tunnel to your development machine works. ARX Messenger loads Mini Apps only over HTTPS.
3. Register the origin with `/setapporigins`, or set a Main Mini App URL with `/setmainapp`, which registers its origin.
4. Give your server the TESTBOT's token so it can validate launch data.

## Run the page outside ARX <a href="#run-the-page-outside-arx" id="run-the-page-outside-arx"></a>

Outside ARX Messenger, `AERX.WebApp` exists but has no native channel: `whenInitialized()` rejects with `UNSUPPORTED`, `initData` is empty and every host method rejects. Use this to build the layout and your fallback screen in a desktop browser.

To check your styles against ARX colors, set the CSS variables yourself, for example `--aerx-theme-bg-color` and `--aerx-theme-text-color`, in light and dark versions. See [Theme and layout](theme-and-layout.md#css-variables).

## Test your server <a href="#test-your-server" id="test-your-server"></a>

Test validation without ARX by signing your own launch data with a test token. Build a URL-encoded string with `bot_id`, `auth_date`, `launch_id`, `launch_kind` and a `user` JSON object, compute `hash` as described in [Validate launch data](validate-launch-data.md#how-the-signature-works), and add placeholder `kid` and `signature` fields. Then test the failures:

- A changed field, such as another user ID, fails the signature check.
- A different bot token fails with a bot mismatch.
- An `auth_date` older than your maximum age is rejected.
- A repeated `launch_id` is rejected by your session store.

Never use a real user's launch data or your production token in tests.

## Test in ARX Messenger <a href="#test-in-arx-messenger" id="test-in-arx-messenger"></a>

Open the app from each entry point you support and check the launch data your server receives.

| Entry point | How to open it | Check |
| --- | --- | --- |
| Menu button | `menu_button.set` with your URL. | `launch_kind` is `menu_button` and `query_id` is present. |
| Inline button | Send a message with a `mini_app` inline button. | `launch_kind` is `inline_button`. |
| Reply keyboard | Send a reply keyboard with a `mini_app` button. | `sendData` delivers a `mini_app_data` message and the app closes. |
| Main Mini App | Tap `https://www.arx.pro/bot/<HANDLE>?startapp=test`. | `launch_kind` is `main_app` and `start_param` is `test`. |
| Named Mini App | Tap `https://www.arx.pro/bot/<HANDLE>/<short_name>`. | `launch_kind` is `direct_link` and `app` is the short name. |

Also check these behaviors:

- Switch the device between light and dark mode while the app is open. Colors follow without a reload.
- Send the app to the background and return. Calls that were pending reject with `SESSION_EXPIRED`, and work again after `activated`.
- Leave the app open past your maximum age, then call `refreshInitData()` before the next request to your server.
- Check `supports()` results on Android and iOS, and confirm your page works when a feature is missing.
- Tap the main button twice quickly. Your page sends one request.

## Debug failures <a href="#debug-failures" id="debug-failures"></a>

| Symptom | Likely cause |
| --- | --- |
| ARX shows an error and your page does not load. | The URL is not on a registered origin, the named app is disabled, or the link is malformed. |
| Bot API returns `mini_app_origin_not_registered`. | Register the URL's origin for this bot. |
| A method rejects with `ORIGIN_NOT_ALLOWED`, or calls time out with `NETWORK_ERROR`. | The page navigated to an origin that is not registered. With relaxed protection only presentation methods work there; otherwise no bridge call is answered. |
| No Mini App of the bot opens. | One of the bot's registered origins uses `http://`. Remove it. |
| A method rejects with `USER_GESTURE_REQUIRED`. | Call it directly from a click handler, not after a delay or a network request. |
| `sendData` rejects with `NOT_ALLOWED_FOR_LAUNCH_KIND`. | The app was not opened from a reply keyboard button. |
| Validation fails on your server. | The page changed `initData`, the token belongs to another bot, the token was rotated, or the data is older than your maximum age. |

When you contact [Support](../support.md), include the operation, the error code and the approximate time. Never include launch data or your token.
