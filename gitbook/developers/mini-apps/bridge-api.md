---
description: "Every property, method, object, event and error of AERX.WebApp, the Mini App bridge."
---

# JavaScript bridge reference

The bridge script `aerx-web-app.js` defines one global object, `window.AERX.WebApp`. Your page uses it to read launch data, control native buttons, store data and use device features. This page describes bridge version 1.0.

## Load the bridge <a href="#load-the-bridge" id="load-the-bridge"></a>

Load the script from ARX with a regular `<script>` tag in `<head>`, before your own code. It must run in the top-level window of your page; frames do not get bridge access.

```html
<script src="https://www.arx.pro/js/aerx-web-app.js"></script>
<script type="module" src="/app.js"></script>
```

This URL always serves the latest bridge. New versions stay compatible with earlier ones; read `AERX.WebApp.version` or use feature detection before you call a newer method.

To pin an exact bridge version instead, load its versioned path with Subresource Integrity. The browser refuses the file if it changes:

```html
<script
  src="https://www.arx.pro/js/mini-apps/5f487ef36452209a50a554244f281e84fab80b6ee5a5fb512849597cb14a45f9/aerx-web-app.js"
  integrity="sha384-0DFoLHxeT+okx4BEJgAgL9/RfqLtsWq58Dg6th6e37/LWWJlZzZmDbZ69o5KtNkv"
  crossorigin="anonymous"></script>
```

A pinned page does not get bridge updates. Move to a newer versioned path when you need a newer feature.

Outside ARX Messenger the script still defines `AERX.WebApp`, but there is no native channel: `whenInitialized()` rejects with `UNSUPPORTED`. Use that to show an "Open in ARX" message.

```js
const app = window.AERX?.WebApp;
try {
  await app.whenInitialized();
} catch {
  document.body.textContent = "Open this page from ARX Messenger.";
}
```

`whenInitialized()` resolves as soon as `initData` is available. `platform`, the supported features and the full theme can arrive a moment later, and ARX emits `themeChanged` once they have arrived. Until then, `platform` is `unknown` and `supports()` returns `false`. Register a `themeChanged` handler before you wait for `whenInitialized()`, and run your feature checks both after initialization and in that handler.

Call `ready()` when your first screen has rendered. ARX keeps its launch screen visible until then. A page that does not use the bridge shortly after loading is shown without waiting.

## Promises, callbacks and errors <a href="#promises-callbacks-and-errors" id="promises-callbacks-and-errors"></a>

Methods that wait for the host return a Promise. Most also accept a callback as their last argument. When you pass a callback, the method returns its owner object so you can chain calls.

| Callback style | Methods | Arguments |
| --- | --- | --- |
| Result | Popups, user requests, managers, sensors | `(value, error)`. On failure, `value` is a fallback such as `null` or `false`. |
| Storage | `CloudStorage`, `DeviceStorage`, `SecureStorage`, `refreshInitData` | `(error, value)`. `error` is `null` on success. |
| Biometric | `BiometricManager.authenticate` | `(isAuthenticated, biometricToken, error)`. |

A rejected Promise carries an `Error` whose `code` is one of the [bridge errors](#errors). A `RATE_LIMITED` error can also carry `retry_after`, the whole seconds to wait.

Invalid arguments throw `INVALID_PARAMS` synchronously, before any message reaches the host. Validate input before you call, or wrap the call in `try`.

A call that gets no answer within 30 seconds rejects with `NETWORK_ERROR`. At most 64 calls can wait at once; more reject with `RATE_LIMITED`. Popups, the invoice sheet and other native sheets wait for the user and do not time out.

ARX processes up to 60 bridge calls per second from a page and ignores the rest without an answer. Throttle repeated button and haptic updates.

## Properties <a href="#properties" id="properties"></a>

All properties are read-only.

| Property | Type | Description |
| --- | --- | --- |
| `initData` | string | The signed launch data. Send it to your server for [validation](validate-launch-data.md). Empty until initialization. |
| `initDataUnsafe` | object | A parsed copy of `initData`. Use it for display only. |
| `version` | `"1.0"` | The bridge version. |
| `platform` | string | `ios`, `android`, `macos`, `windows`, `linux`, `web` or `unknown`. |
| `paymentPolicy` | object or `null` | `digital_goods_allowed` and `external_link_allowed` for this ARX build. |
| `colorScheme` | `"light"` or `"dark"` | The user's current color scheme. |
| `themeParams` | object | Theme colors as `#rrggbb`. See [Theme and layout](theme-and-layout.md). |
| `isActive` | boolean | `false` while the app is in the background. |
| `isExpanded` | boolean | `true` when the app uses its full height. |
| `viewportHeight` | number | The current visible height in CSS pixels. |
| `viewportStableHeight` | number | The height after the last completed resize. |
| `headerColor`, `backgroundColor`, `bottomBarColor` | string | The resolved colors set with the matching setters. |
| `isClosingConfirmationEnabled` | boolean | Whether closing asks the user to confirm. |
| `isVerticalSwipesEnabled` | boolean | Whether a vertical swipe can minimize or close the app. |
| `isFullscreen` | boolean | Whether the app is in full screen. |
| `isOrientationLocked` | boolean | Whether the orientation is locked. |
| `safeAreaInset`, `contentSafeAreaInset` | object | `top`, `bottom`, `left` and `right` in CSS pixels. |
| `performanceClass` | string | `LOW`, `AVERAGE` or `HIGH`. Use it to scale animations. |

The objects `MainButton`, `SecondaryButton`, `BackButton`, `SettingsButton`, `CloudStorage`, `DeviceStorage`, `SecureStorage`, `BiometricManager`, `LocationManager`, `Accelerometer`, `Gyroscope`, `DeviceOrientation` and `HapticFeedback` are described below.

## Feature detection <a href="#feature-detection" id="feature-detection"></a>

`supports(feature)` returns `true` when this client and launch support a feature. Check it before calling a method that needs one. Without the feature, the method rejects with `UNSUPPORTED`.

| Feature | Methods |
| --- | --- |
| `fullscreen` | `requestFullscreen`, `exitFullscreen`. |
| `orientation_lock` | `lockOrientation`, `unlockOrientation`. |
| `haptics` | `HapticFeedback`. |
| `device_storage` | `DeviceStorage`. |
| `secure_storage` | `SecureStorage`. |
| `biometrics` | `BiometricManager` methods after `init`. |
| `location` | `LocationManager` methods after `init`. |
| `accelerometer`, `gyroscope`, `device_orientation` | The matching sensor. |
| `qr_scanner` | `showScanQrPopup`, `closeScanQrPopup`. |
| `clipboard_read` | `readTextFromClipboard`. |
| `home_screen` | `addToHomeScreen`. |
| `invoice` | `openInvoice`. |
| `digital_invoice` | `openInvoice` for digital goods. Present only when `paymentPolicy.digital_goods_allowed` is `true`. |

`isVersionAtLeast(version)` compares a dotted version such as `"1.0"` with the bridge version.

## Lifecycle and view <a href="#lifecycle-and-view" id="lifecycle-and-view"></a>

| Method | Description |
| --- | --- |
| `whenInitialized()` | Resolves with `WebApp` once launch data is available. |
| `ready()` | Tells ARX your page is ready to show. |
| `expand()` | Expands a compact app to full height. |
| `close()` | Closes the app. It also accepts `{ return_back: boolean }`, which currently has no additional effect. |
| `refreshInitData(callback?)` | Resolves with newly signed `initData` and updates `initData` and `initDataUnsafe`. Limited to one call per minute. |
| `setHeaderColor(color)`, `setBackgroundColor(color)` | `#RRGGBB`, `bg_color` or `secondary_bg_color`. |
| `setBottomBarColor(color)` | `#RRGGBB`, `bg_color`, `secondary_bg_color` or `bottom_bar_bg_color`. |
| `enableClosingConfirmation()`, `disableClosingConfirmation()` | Asks the user to confirm before closing. |
| `enableVerticalSwipes()`, `disableVerticalSwipes()` | Allows or prevents closing the app with a vertical swipe. |
| `hideKeyboard()` | Hides the on-screen keyboard and blurs the focused field. |
| `requestFullscreen()`, `exitFullscreen()` | Needs `fullscreen`. Emits `fullscreenChanged` or `fullscreenFailed`. |
| `lockOrientation()`, `unlockOrientation()` | Needs `orientation_lock`. |

## Popups <a href="#popups" id="popups"></a>

| Method | Description |
| --- | --- |
| `showPopup(params, callback?)` | Shows a native dialog. Resolves with the pressed button's `id`, or `null`. |
| `showAlert(message, callback?)` | Shows a message with a Close button. |
| `showConfirm(message, callback?)` | Shows OK and Cancel. Resolves with `true` for OK. |

`showPopup` takes `title` (up to 64 characters), `message` (1 to 256 characters) and `buttons`, one to three objects with `id` (up to 64 characters), `type` and `text`. `type` is `default`, `ok`, `close`, `cancel` or `destructive`. `default` and `destructive` buttons need `text` of 1 to 64 characters. Without `buttons`, the popup shows one Close button. You can open at most three popups in three seconds, and only one at a time.

## Links and navigation <a href="#links-and-navigation" id="links-and-navigation"></a>

These methods need a recent user tap on your page. Call them from a click handler.

| Method | Description |
| --- | --- |
| `openLink(url)` | Opens an `https`, `http` or `mailto` URL outside ARX. The tap must be less than 1 second old and is used up by the call. |
| `openAERXLink(url)` | Opens an `https://www.arx.pro/` link inside ARX, such as a bot or profile link. Login links are rejected. |
| `switchInlineQuery(query, chatTypes?)` | Closes the app and opens a chat with `@<HANDLE> <query>` in the message field. Without `chatTypes`, ARX uses the current chat when there is one. `chatTypes` lets the user pick a chat of the listed kinds: `users`, `bots`, `groups` or `channels`. `query` has up to 256 characters. Inline mode is [not available yet](../not-available-yet.md), so this method cannot open inline mode for your bot today. |
| `downloadFile({ url, file_name })` | Asks the user to download an HTTPS file. `file_name` has 1 to 255 characters without slashes. Resolves `true` when the download starts. |
| `addToHomeScreen()` | Needs `home_screen`. Offers a home screen shortcut to your app. |
| `checkHomeScreenStatus(callback?)` | Resolves with `unsupported`, `unknown`, `added` or `missed`. |

URLs have up to 2,048 characters and cannot contain a user name or password. For all methods except `openLink`, the tap can be up to 10 seconds old.

## Data and user requests <a href="#data-and-user-requests" id="data-and-user-requests"></a>

| Method | Description |
| --- | --- |
| `sendData(data)` | Sends 1 to 4,096 UTF-8 bytes to your bot as a `mini_app_data` service message, then closes the app. Only for `keyboard_button` launches, once per launch. See [Send results to your bot](send-data.md). |
| `shareMessage(id, callback?)` | Lets the user share a message your bot prepared with `prepared_messages.save`. Needs a recent tap. Listen for `shareMessageSent` or `shareMessageFailed` to learn the outcome. |
| `requestChat(id, callback?)` | Shows a button your bot prepared with `prepared_buttons.save`, such as a user picker. Resolves `true` when the selection is sent. Needs a recent tap. |
| `requestWriteAccess(callback?)` | Asks the user to let your bot message them. Resolves `true` when allowed. Your bot receives a `write_access_allowed` service message. |
| `requestContact({ request_public_address }, callback?)` | Asks the user to share their ARX card with your bot. Resolves `true` when shared. |
| `readTextFromClipboard(callback?)` | Needs `clipboard_read` and a recent tap. Available to apps opened from the attachment or side menu. Resolves with text or `null`. |
| `openInvoice(url, callback?)` | Needs `invoice` and a tap less than 1 second old, which the call uses up. Opens an invoice link from `invoice_links.create` in the native checkout. `url` is `https://www.arx.pro/invoice/<slug>` or the slug alone. Resolves with `paid`, `pending`, `cancelled` or `failed`. Deliver the order only after your bot receives the payment update. See [Payments](../mini-apps.md#payments). |

A Mini App can make at most five write-access and contact requests per opening, and 20 per user per day. Further requests reject with `RATE_LIMITED`.

## Scan QR codes <a href="#scan-qr-codes" id="scan-qr-codes"></a>

`showScanQrPopup({ text }, callback?)` opens the native scanner with an optional hint of up to 64 characters. It needs `qr_scanner`.

- Without a callback, it resolves with the first code, or `null` when the user closes the scanner.
- With a callback, your callback receives each code. Return `true` to close the scanner.

`closeScanQrPopup()` closes it. Only one scanner can be open.

## MainButton and SecondaryButton <a href="#mainbutton-and-secondarybutton" id="mainbutton-and-secondarybutton"></a>

`MainButton` and `SecondaryButton` are native buttons at the bottom of the app. Each method returns the button, so you can chain calls.

| Member | Description |
| --- | --- |
| `setText(text)` | Sets the label, up to 64 characters. An empty label hides the button. |
| `setParams(params)` | Sets `text`, `color`, `text_color` (`#RRGGBB`), `is_visible`, `is_active`, `is_progress_visible`, `has_shine_effect` and `position` (`left`, `right`, `top` or `bottom`). |
| `show()`, `hide()` | Shows or hides the button. |
| `enable()`, `disable()` | Allows or blocks taps. |
| `showProgress(leaveActive?)`, `hideProgress()` | Shows a spinner. The button is disabled unless `leaveActive` is `true`. |
| `onClick(handler)`, `offClick(handler)` | Adds or removes a click handler. |
| `type`, `text`, `color`, `textColor`, `isVisible`, `isActive`, `isProgressVisible`, `hasShineEffect`, `position` | Current state. |

If you never set the label, ARX shows a localized default.

## BackButton and SettingsButton <a href="#backbutton-and-settingsbutton" id="backbutton-and-settingsbutton"></a>

`BackButton` appears in the app header. `SettingsButton` appears in the Mini App menu when you enable it with `/setappsettings` in NexoCreator. Both have `show()`, `hide()`, `onClick(handler)`, `offClick(handler)` and `isVisible`.

## CloudStorage <a href="#cloudstorage" id="cloudstorage"></a>

`CloudStorage` stores strings for the current user and your bot on ARX servers. It does not need a feature check.

| Method | Description |
| --- | --- |
| `setItem(key, value, callback?)` | Stores a value of up to 4,096 UTF-16 code units (for example, 2,048 emoji represented by surrogate pairs). |
| `getItem(key, callback?)` | Resolves with the value, or an empty string when the key is not set. |
| `getItems(keys, callback?)` | Resolves with an object for up to 100 keys. |
| `removeItem(key, callback?)`, `removeItems(keys, callback?)` | Deletes one key or up to 100 keys. |
| `getKeys(callback?)` | Resolves with all keys. |

Keys have 1 to 128 characters: letters, digits, underscores and hyphens. Each user can store up to 1,024 keys for your bot; a write beyond that rejects with `QUOTA_EXCEEDED`. By default ARX allows 300 reads and 60 writes per minute; more reject with `RATE_LIMITED`.

## DeviceStorage <a href="#devicestorage" id="devicestorage"></a>

`DeviceStorage` keeps strings on the current device only. It needs `device_storage`. It has `setItem`, `getItem`, `removeItem` and `clear`. `setItem(key, null)` removes a key. All keys and values together can use up to 5 MiB; a write beyond that rejects with `QUOTA_EXCEEDED`.

## SecureStorage <a href="#securestorage" id="securestorage"></a>

`SecureStorage` keeps up to 10 values in the device's protected storage. It needs `secure_storage`. Values have up to 4,096 characters.

| Method | Description |
| --- | --- |
| `setItem(key, value, callback?)` | Stores a value. |
| `getItem(key, callback?)` | Resolves with `{ value, canRestore }`. `value` is `null` when the key is not set. The callback receives `(error, value, canRestore)`. |
| `removeItem(key, callback?)`, `clear(callback?)` | Deletes one or all values. |
| `restoreItem(key, callback?)` | Not available. Rejects with `UNSUPPORTED`. |

## BiometricManager <a href="#biometricmanager" id="biometricmanager"></a>

Call `init()` first. It resolves with the manager and fills in its properties. The other methods need `biometrics` and throw `INVALID_PARAMS` before `init()` completes.

| Member | Description |
| --- | --- |
| `init(callback?)` | Reads biometric availability. |
| `requestAccess({ reason }, callback?)` | Asks permission to use biometrics. `reason` has up to 128 characters. Resolves with `isAccessGranted`. |
| `authenticate({ reason }, callback?)` | Asks the user to authenticate. Resolves with `{ isAuthenticated, biometricToken }`. |
| `updateBiometricToken(token, callback?)` | Stores a token of up to 1,024 characters behind biometrics. Resolves `true` when updated. |
| `openSettings()` | Opens the app's biometric settings. Needs a recent tap. |
| `isInited`, `isBiometricAvailable`, `biometricType`, `isAccessRequested`, `isAccessGranted`, `isBiometricTokenSaved`, `deviceId` | Current state. `biometricType` is `finger`, `face` or `unknown`. |

## LocationManager <a href="#locationmanager" id="locationmanager"></a>

Call `init()` first. The other methods need `location`.

| Member | Description |
| --- | --- |
| `init(callback?)` | Reads location availability. |
| `getLocation(callback?)` | Asks for the user's location. Resolves with `latitude`, `longitude`, `altitude`, `course`, `speed`, `horizontal_accuracy`, `vertical_accuracy`, `course_accuracy` and `speed_accuracy`, or `null`. Missing values are `null`. |
| `openSettings()` | Opens the location settings. Needs a recent tap. |
| `isInited`, `isLocationAvailable`, `isAccessRequested`, `isAccessGranted` | Current state. |

## Motion sensors <a href="#motion-sensors" id="motion-sensors"></a>

`Accelerometer` and `Gyroscope` expose `x`, `y` and `z`. `DeviceOrientation` exposes `absolute`, `alpha`, `beta` and `gamma`. Each needs its feature.

`start({ refresh_rate }, callback?)` starts updates every `refresh_rate` milliseconds, from 20 to 1,000 (default 1,000). `DeviceOrientation.start` also accepts `need_absolute`. `stop(callback?)` stops updates. `isStarted` shows the state. Sensors stop when the app goes to the background; start them again after `activated`.

## HapticFeedback <a href="#hapticfeedback" id="hapticfeedback"></a>

Needs `haptics`. Methods return `HapticFeedback` for chaining.

| Method | Values |
| --- | --- |
| `impactOccurred(style)` | `light`, `medium`, `heavy`, `rigid` or `soft`. |
| `notificationOccurred(type)` | `success`, `warning` or `error`. |
| `selectionChanged()` | No arguments. |

## Events <a href="#events" id="events"></a>

Subscribe with `onEvent(name, handler)` and remove a handler with `offEvent(name, handler)`. Inside a handler, `this` is `WebApp`. An error thrown by one handler does not stop the others.

| Event | Payload |
| --- | --- |
| `activated`, `deactivated` | None. The app returned to or left the foreground. |
| `themeChanged` | None that you need. Read `themeParams` and `colorScheme`. |
| `viewportChanged` | `{ isStateStable }`. Read `viewportHeight` and `viewportStableHeight`. |
| `safeAreaChanged`, `contentSafeAreaChanged` | `{ top, bottom, left, right }`. |
| `mainButtonClicked`, `secondaryButtonClicked`, `backButtonClicked`, `settingsButtonClicked` | None. |
| `popupClosed` | `{ button_id }`. |
| `fullscreenChanged` | `{ isFullscreen }`. |
| `fullscreenFailed` | `{ error }`: `UNSUPPORTED` or `ALREADY_FULLSCREEN`. |
| `qrTextReceived` | `{ data }`. |
| `scanQrPopupClosed` | None. |
| `clipboardTextReceived` | `{ data }`. |
| `writeAccessRequested` | `{ status }`: `allowed` or `cancelled`. |
| `contactRequested` | `{ status }`: `sent` or `cancelled`. |
| `shareMessageSent`, `requestedChatSent` | None. |
| `shareMessageFailed`, `requestedChatFailed` | `{ error }`. |
| `fileDownloadRequested` | `{ status }`: `downloading` or `cancelled`. |
| `invoiceClosed` | `{ url, slug, status }`. `status` is `paid`, `pending`, `cancelled` or `failed`. |
| `homeScreenAdded` | None. |
| `homeScreenChecked` | `{ status }`. |
| `biometricManagerUpdated`, `locationManagerUpdated` | The manager's properties changed. |
| `biometricAuthRequested` | `{ isAuthenticated, biometricToken }`. |
| `biometricTokenUpdated` | `{ isUpdated }`. |
| `locationRequested` | `{ locationData }`. |
| `accelerometerStarted`, `accelerometerStopped`, `gyroscopeStarted`, `gyroscopeStopped`, `deviceOrientationStarted`, `deviceOrientationStopped` | None. |
| `accelerometerChanged`, `gyroscopeChanged` | `{ x, y, z }`. |
| `deviceOrientationChanged` | `{ absolute, alpha, beta, gamma }`. |
| `accelerometerFailed`, `gyroscopeFailed`, `deviceOrientationFailed` | `{ error: "UNSUPPORTED" }`. |

When the app goes to the background, ARX sends `deactivated`. Pending calls reject with `SESSION_EXPIRED`, new calls reject the same way, and button clicks and sensor samples stop. After `activated`, ARX sends fresh launch data and theme, and calls work again. If the app stayed in the background for more than about two minutes, the fresh launch data belongs to a new launch with a new `launch_id`. Launches with a `query_id` cannot resume: ARX closes the app, and the query expires.

## Errors <a href="#errors" id="errors"></a>

| Code | Meaning |
| --- | --- |
| `UNSUPPORTED` | This client, launch or feature does not support the method, or the page runs outside ARX. |
| `ORIGIN_NOT_ALLOWED` | The page is not on one of your bot's registered origins. |
| `USER_GESTURE_REQUIRED` | Call the method from a recent user tap. |
| `NOT_ALLOWED_FOR_LAUNCH_KIND` | The method is not available for this `launch_kind`, for example `sendData` outside a reply keyboard launch. |
| `SESSION_EXPIRED` | The app is in the background or the opening ended. |
| `RATE_LIMITED` | Too many calls, or the same kind of request is already in progress. Check `retry_after`. |
| `INVALID_PARAMS` | An argument is missing, too long or has the wrong type. |
| `QUOTA_EXCEEDED` | A storage limit was reached. |
| `PRIVACY_POLICY_REQUIRED` | Your bot needs a privacy policy for this request. Set one with `/setprivacypolicy`. |
| `USER_DECLINED` | The user declined the request. |
| `NETWORK_ERROR` | The host did not answer in time or the network failed. You can retry. |
| `UNKNOWN_ERROR` | Any other failure. |

## Methods that are not available <a href="#methods-that-are-not-available" id="methods-that-are-not-available"></a>

These methods exist so that feature checks work. They always reject with `UNSUPPORTED`: `requestWalletSignature`, `setEmojiStatus`, `requestEmojiStatusAccess`, `shareToStory`, `reportAgeVerification`, `requestLogin` and `SecureStorage.restoreItem`. Their arguments are still validated, so invalid input throws `INVALID_PARAMS` first.

`AERX.WebApp` also has methods reserved for ARX's own apps. They reject with `UNSUPPORTED` in your Mini App.
