---
description: "Build a web app that opens inside ARX Messenger and works with your bot."
---

# Mini Apps

A Mini App is a web page that your bot opens inside ARX Messenger. The page runs in an in-app web view, follows the user's theme and uses native controls such as the main button and back button. Your bot can open it from a menu button, a keyboard button or a link, and the page can return data to your bot.

## How it works <a href="#how-it-works" id="how-it-works"></a>

1. You host the page on an HTTPS origin and register that origin for your bot in NexoCreator.
2. A user opens the Mini App from your bot. ARX checks that the URL belongs to a registered origin, then loads it.
3. ARX signs launch data for this opening. The page receives it through the JavaScript bridge as `AERX.WebApp.initData`.
4. The page sends `initData` to your server. Your server verifies the signature with your bot token before it trusts the user.
5. The page uses the bridge for native controls, storage and device features. It can send a result back to the chat through your bot.

The bridge is the only channel between your page and ARX. The signed launch data never appears in the page URL.

## Requirements <a href="#requirements" id="requirements"></a>

- A bot that you own in NexoCreator. A TESTBOT works for development; see [Test a Mini App](mini-apps/testing.md).
- A public HTTPS origin that serves your page, such as `https://app.example.com`. Register up to 20 origins per bot.
- The ARX bridge script, loaded from `https://www.arx.pro/js/aerx-web-app.js` before your own code. See [Load the bridge](mini-apps/bridge-api.md#load-the-bridge).
- A server that holds your bot token and verifies launch data. Never put the token in the page.

## A minimal page <a href="#a-minimal-page" id="a-minimal-page"></a>

This page waits for the bridge, shows the user's name, and offers a main button that closes the app.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
  <title>Order status</title>
  <script src="https://www.arx.pro/js/aerx-web-app.js"></script>
  <style>
    body {
      margin: 0;
      padding: 16px;
      font-family: system-ui, sans-serif;
      background: var(--aerx-theme-bg-color, #ffffff);
      color: var(--aerx-theme-text-color, #000000);
    }
  </style>
</head>
<body>
  <h1 id="title">Opening…</h1>
  <script>
    const app = window.AERX && window.AERX.WebApp;
    if (!app) {
      document.getElementById("title").textContent = "Open this page from ARX.";
    } else {
      app.whenInitialized().then(() => {
        const user = app.initDataUnsafe.user;
        document.getElementById("title").textContent = user ? "Hello, " + user.name : "Hello";
        app.MainButton.setText("Done").onClick(() => app.close()).show();
        app.ready();
      });
    }
  </script>
</body>
</html>
```

`initDataUnsafe` is fine for choosing what to display. It is not proof of identity. Send `initData` to your server and [validate it](mini-apps/validate-launch-data.md) before you read or change the user's data.

## Launch kinds <a href="#launch-kinds" id="launch-kinds"></a>

Every opening has a signed `launch_kind`. It tells you where the user opened the app and which bridge methods are available.

| `launch_kind` | Opened from | Notes |
| --- | --- | --- |
| `menu_button` | Your bot's menu button, set to a Mini App URL. | Launch data includes `query_id`. |
| `inline_button` | A `mini_app` button in an inline keyboard. | Launch data includes `query_id`. |
| `keyboard_button` | A `mini_app` button in a reply keyboard, in a private chat with your bot. | The only launch kind that can call `sendData`. |
| `main_app` | Your bot's Main Mini App, for example from `https://www.arx.pro/bot/<HANDLE>?startapp=`. | Launch data includes `chat_type`, `chat_instance` and an optional `start_param`. |
| `direct_link` | A named Mini App link, `https://www.arx.pro/bot/<HANDLE>/<short_name>`. | Also includes `app`, the named app's short name. |
| `attach_menu`, `side_menu` | The attachment or side menu, after ARX reviews your app. | `attach_menu` includes `query_id`. |
| `inline_mode` | The Mini App button above your bot's inline results. | No `query_id`. Inline mode is [not available yet](not-available-yet.md), so you do not receive this kind today. |
| `join_request` | The applicant's guard check, opened with `join_request_queries.open_mini_app`. | Includes `chat`, `chat_join_request` and `chat_join_request_query_id`. See [Guard join requests](groups/invites-and-requests.md#guard-join-requests). |

[Launch a Mini App](mini-apps/launch.md) shows how to create each entry point.

## Payments <a href="#payments" id="payments"></a>

A Mini App can open an invoice link that your bot created with [`invoice_links.create`](payments/invoices.md). Check `supports("invoice")`, then call `openInvoice(url)` from a tap. `url` is the link's `https://www.arx.pro/invoice/<slug>` value, or the slug alone. The buyer pays in the native checkout, and your bot answers the pre-checkout query as for any other invoice. The Promise resolves with `paid`, `pending`, `cancelled` or `failed`, and ARX emits `invoiceClosed` with `{ url, slug, status }`. Treat the result as a hint: deliver the order only after your bot receives the payment update.

`requestWalletSignature` is not available and always rejects with `UNSUPPORTED`.

`AERX.WebApp.paymentPolicy` tells you what the current ARX build allows you to offer. It contains `digital_goods_allowed` and `external_link_allowed`. Both are `false` on app-store builds of ARX Messenger. Hide purchase prompts and external payment links when the matching value is `false`.

## Next steps <a href="#next-steps" id="next-steps"></a>

- [Register a Mini App](mini-apps/register.md) in NexoCreator.
- [Launch a Mini App](mini-apps/launch.md) from a menu button, a keyboard button or a link.
- [Validate launch data](mini-apps/validate-launch-data.md) on your server.
- Read the [JavaScript bridge reference](mini-apps/bridge-api.md).
- Style your page with [Theme and layout](mini-apps/theme-and-layout.md).
- Use [Buttons, popups and closing](mini-apps/buttons.md) for native controls.
- [Send results to your bot](mini-apps/send-data.md).
- Review the [security checklist](mini-apps/security.md) and [test your Mini App](mini-apps/testing.md).
