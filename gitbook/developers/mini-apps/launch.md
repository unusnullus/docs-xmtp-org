---
description: "Open your Mini App from the menu button, keyboard buttons and links, and read the launch context."
---

# Launch a Mini App

You can open a Mini App from your bot's menu button, from a button on a message, from a reply keyboard, or from a link. Each entry point sets a different `launch_kind` in the signed launch data, and some bridge methods depend on it.

Every URL you pass must use HTTPS and belong to an origin registered for your bot. See [Register a Mini App](register.md#allowed-origins).

## Menu button <a href="#menu-button" id="menu-button"></a>

Call `menu_button.set` with a `mini_app` body to replace the menu button next to the message field. Omit the `chat_id` query to set the bot-wide button, or pass a private chat ID to set it for one chat.

| Field | Rule |
| --- | --- |
| `type` | `mini_app`. |
| `text` | The button label: 1 to 64 UTF-16 units, not only whitespace, no control characters. |
| `url` | Up to 2,048 characters, on a registered origin. |

Set `ARX_BOT_TOKEN` and `REQUEST_KEY` before running the example. Replace the URL with a page on your registered origin.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
if (!token || !key) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const result = await bot.menuButtonSet({
		key,
		body: { type: "mini_app", text: "Open shop", url: "https://app.example.com/shop" },
	});
	console.log(result);
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models

with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.MenuButton.model_validate(
        {"type": "mini_app", "text": "Open shop", "url": "https://app.example.com/shop"}
    )
    result = bot.menu_button_set(body=request, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let request = models::MenuButton::MenuButtonVariant2(Box::new(
        models::MenuButtonVariant2::new("Open shop".into(), "https://app.example.com/shop".into()),
    ));
    let result = bot.menu_button_set(&[], Some(&request), Some(&key)).await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The app opens with `launch_kind` set to `menu_button`, and the launch data includes a `query_id` for [answering from your server](send-data.md#answer-a-mini-app-query). If the URL is your own bot's Main Mini App or named Mini App link, such as `https://www.arx.pro/bot/<HANDLE>?startapp=menu`, ARX opens that app instead, with `launch_kind` set to `main_app` or `direct_link`.

Use `{"type":"commands"}` or `{"type":"default"}` to go back to the command menu. A chat-specific `default` falls back to the bot-wide button. See [Commands and profile](../commands-and-profile.md#configure-the-menu-button) and the [`menu_button.set` reference](../reference/profile.md#operation-menu-button-set).

## Inline keyboard button <a href="#inline-keyboard-button" id="inline-keyboard-button"></a>

Add a button with `type` set to `mini_app` to an inline keyboard. It accepts `text`, `url` and an optional `style` (`primary`, `success` or `danger`), with the same label and URL rules as other inline buttons. See [Keyboards](../keyboards.md) for keyboard limits.

Set `ARX_BOT_TOKEN`, `CHAT_ID` and `REQUEST_KEY` before running the example.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, InlineKeyboard } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
if (!token || !key || !chatId) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const result = await bot.messagesSend(chatId, {
		key,
		body: {
			content: { type: "text", text: "Track your order in the app." },
			reply_markup: new InlineKeyboard()
				.row({
					type: "mini_app",
					text: "Track order",
					url: "https://app.example.com/orders/42",
					style: "primary",
				})
				.build(),
		},
	});
	console.log(result);
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, FormattedText, InlineKeyboard, models

chat_id = os.environ["CHAT_ID"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    button = models.InputInlineButton.model_validate(
        {
            "type": "mini_app",
            "text": "Track order",
            "url": "https://app.example.com/orders/42",
            "style": "primary",
        }
    )
    request = models.SendMessage(
        content=FormattedText("Track your order in the app.").content(),
        reply_markup=InlineKeyboard().row(button).markup(),
    )
    result = bot.messages_send(chat_id, body=request, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, FormattedText, InlineKeyboard, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let mut mini_app = models::InputInlineButtonMiniApp::new(
        "Track order".into(),
        "https://app.example.com/orders/42".into(),
    );
    mini_app.style = Some(models::InputInlineButtonMiniAppStyle::Primary);
    let button = models::InputInlineButton::InputInlineButtonMiniApp(Box::new(mini_app));
    let keyboard = InlineKeyboard::new().row(vec![button])?;
    let request = models::SendMessage {
        reply_markup: Some(keyboard.markup()?),
        ..models::SendMessage::new(FormattedText::new("Track your order in the app.", vec![])?.content()?)
    };
    let result = bot
        .messages_send(&chat_id, &[], Some(&request), Some(&key))
        .await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The app opens with `launch_kind` set to `inline_button` and a `query_id`. A message with a Mini App button on an unregistered origin fails with HTTP 400 `mini_app_origin_not_registered`.

## Reply keyboard button <a href="#reply-keyboard-button" id="reply-keyboard-button"></a>

A reply keyboard button with `type` set to `mini_app` opens the app from a private chat with your bot. This is the only entry point where the page can call `sendData` to post a result into the chat. The button accepts `text`, `url` and an optional `style`. [Send results to your bot](send-data.md#send-data-from-a-reply-keyboard-button) has a complete example.

## Links <a href="#links" id="links"></a>

ARX Messenger opens these links as Mini Apps when a user taps them. Use your bot's current handle.

| Link | Opens | `launch_kind` |
| --- | --- | --- |
| `https://www.arx.pro/bot/<HANDLE>?startapp=` | The Main Mini App. | `main_app` |
| `https://www.arx.pro/bot/<HANDLE>?startapp=<param>` | The Main Mini App with a start parameter. | `main_app` |
| `https://www.arx.pro/bot/<HANDLE>/<short_name>` | A named Mini App. | `direct_link` |
| `https://www.arx.pro/bot/<HANDLE>/<short_name>?startapp=<param>` | A named Mini App with a start parameter. | `direct_link` |
| `https://www.arx.pro/bot/<HANDLE>?startattach=<param>` | Your app in the attachment menu, after ARX approves the placement. Add `peer=<HANDLE>` to open it in a chat with that bot, or `choose=users+groups` to let the user pick a chat. `choose` accepts `users`, `bots`, `groups` and `channels`. | `attach_menu` |

Add `mode=compact` or `mode=fullscreen` to request a launch mode, for example `https://www.arx.pro/bot/ACMESHOPBOT?startapp=spring&mode=fullscreen`. Other query parameters make the link invalid. Handles in links have 8 to 24 characters.

The start parameter contains up to 512 ASCII letters, digits, underscores or hyphens. ARX passes a non-empty value in two places: the signed `start_param` field of the launch data, and the `aerxStartParam` query parameter of your page URL. Use the signed value for decisions on your server. Treat both as user input, because anyone can write a link.

## Launch data <a href="#launch-data" id="launch-data"></a>

The launch data is a URL-encoded string of signed fields. The page reads it as `AERX.WebApp.initData` and a parsed copy as `AERX.WebApp.initDataUnsafe`. JSON fields such as `user` arrive as JSON strings in `initData` and as objects in `initDataUnsafe`.

| Field | Present | Value |
| --- | --- | --- |
| `bot_id` | Always | Your bot's ID. |
| `launch_kind` | Always | One of the launch kinds above. |
| `launch_id` | Always | A unique ID for this opening. It stays the same after `refreshInitData()`. Use it with `auth_date` to detect replays. |
| `auth_date` | Always | The signing time in Unix seconds. |
| `user` | Always | JSON object: `id`, `name`, `username`, and optionally `language_code`, `is_premium`, `added_to_attachment_menu`, `allows_write_to_pm`, `photo_url`. |
| `payment_policy` | Always | JSON object with `digital_goods_allowed` and `external_link_allowed`. |
| `query_id` | `menu_button`, `inline_button`, `attach_menu` | A 32-character ID for `mini_app_queries.answer`. `inline_mode` and `join_request` launches have no `query_id`. |
| `can_send_after` | With `query_id` | Seconds to wait before answering the query. |
| `chat_type` | `main_app`, `direct_link` | `sender` when the user opened the app from their chat with your bot or from no chat, `private` when they opened it from another private chat, `group` when they opened it in a group. |
| `chat_instance` | With `chat_type` | An opaque identifier for the chat the app was opened from. |
| `app` | `direct_link` | The named Mini App's short name. |
| `start_param` | `main_app`, `direct_link`, `attach_menu` | The link's start parameter, when it is not empty. |
| `receiver` | `attach_menu` | The bot whose private chat the user opened the attachment menu in. |
| `chat` | `join_request` | JSON object for the group or channel: `id`, `kind` (`group` or `channel`), `instance`, `title` and optional `username`. |
| `chat_join_request` | `join_request` | JSON object: `request_id`, `deadline_at` (RFC 3339, UTC) and `origin`. |
| `chat_join_request_query_id` | `join_request` | The `query_id` to answer with `join_request_queries.answer`. |
| `hash`, `kid`, `signature` | Always | Signature fields. See [Validate launch data](validate-launch-data.md). |

`allows_write_to_pm` is true when your bot may send messages to the user. Treat unknown fields as extensions: keep them in the signed string when you verify, and ignore them otherwise.

A launch stays valid while the app is open: ARX renews its session every minute, for up to 24 hours. If the page stays open longer than your maximum age, call `AERX.WebApp.refreshInitData()` before you send launch data to your server. The refreshed data keeps the same `launch_id` and has a newer `auth_date`. ARX allows one refresh per minute.

If the app stays in the background for more than about two minutes, ARX starts a new launch when the user returns, with a new `launch_id`. Launches with a `query_id` cannot resume: ARX closes the app, and the query expires.

## Launch errors <a href="#launch-errors" id="launch-errors"></a>

When ARX cannot open the app, the user sees an error in Messenger and your page does not load. The common causes are a URL on an unregistered origin, a disabled or deleted named app, a missing Main Mini App for a `startapp` link, and a link with an invalid handle, short name or start parameter.
