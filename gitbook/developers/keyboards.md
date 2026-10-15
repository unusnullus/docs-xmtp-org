---
description: "Add message buttons, answer callbacks and collect input with reply keyboards."
---

# Keyboards and callbacks

You can add buttons to a message so users can choose an action or share input. Inline keyboards belong to one message. Reply keyboards offer inputs for the conversation.

## Send an inline keyboard <a href="#send-an-inline-keyboard" id="send-an-inline-keyboard"></a>

Add `reply_markup` beside `content` in `messages.send`. This example sends a confirmation button.

Set `ARX_BOT_TOKEN`, `CHAT_ID` and `REQUEST_KEY` before running the example. Use a private chat that the user has started. See [Making requests](making-requests.md) for choosing keys and retrying requests.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, InlineButton, InlineKeyboard } from "@arx/nexo";

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
			content: { type: "text", text: "Save this reminder?" },
			reply_markup: new InlineKeyboard()
				.row(
					InlineButton.callback("Save reminder", "reminder:save:42", {
						style: "primary",
					}),
				)
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
from arx_nexo import Client, Config, models, FormattedText, InlineButton, InlineKeyboard

chat_id = os.environ["CHAT_ID"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.SendMessage(
        content=FormattedText("Save this reminder?").content(),
        reply_markup=InlineKeyboard()
        .row(
            InlineButton.callback("Save reminder", "reminder:save:42", style="primary")
        )
        .markup(),
    )
    result = bot.messages_send(
        chat_id,
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
    print(result)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{
    ButtonStyle, Client, Config, FormattedText, InlineButton, InlineKeyboard, models,
    presence::Presence,
};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let keyboard = InlineKeyboard::new().row(vec![InlineButton::callback(
        "Save reminder",
        "reminder:save:42",
        Some(ButtonStyle::Primary),
    )?])?;
    let request = models::SendMessage {
        content: FormattedText::new("Save this reminder?", vec![])?.content()?,
        effect_id: None,
        link_preview: Presence::Unset,
        protect_content: None,
        reply_markup: Some(keyboard.markup()?),
        reply_to: None,
        silent: None,
    };
    let result = bot
        .messages_send(&chat_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

| Inline button | Required action field | What the user gets |
| --- | --- | --- |
| `callback` | `data` | Your bot receives a `callback_query` update. |
| `url` | `url` | The user opens a URL or an ARX profile link. |
| `copy_text` | `copy` | The user copies text. |
| `disabled` | None | The button shows a label without an action. |

Every button requires `type` and `text`. An inline keyboard permits up to 100 buttons. Each row holds one to eight buttons; only `rows` itself can be empty. Labels contain 1 to 64 UTF-16 units. A callback's `data` contains 1 to 64 UTF-8 bytes, while `copy` contains 1 to 256 UTF-16 units. See [Formatting](formatting.md#utf-16-offsets) for how to count text.

You can set `style` to `primary`, `success` or `danger`. Omit it to use the default appearance. A URL button with `aerx://user?id=42` becomes a profile action for that user ID. For links that start your bot, see [Deep links](deep-links.md).

## Receive and answer a callback <a href="#receive-and-answer-a-callback" id="receive-and-answer-a-callback"></a>

A `callback_query` contains `id`, `user`, `message`, `chat_instance`, `data` and `created_at`. Both `message` and `data` are required. If the original message is no longer readable, `message.kind` is `inaccessible`; check its kind before reading content.

Validate the action and the user's permission to perform it. A press can arrive again, and the underlying reminder may already have changed. Use the [update delivery rules](updates.md#order-and-duplicates) to avoid repeating an action.

Call `callback_queries.answer` for every callback, including actions that need no visible reply. Use the callback's `id` as the path argument. The HTTP request body can be `{}` or, for example:

```json
{"text":"Reminder saved","show_alert":false,"cache_seconds":0}
```

The typed methods are `callbackQueriesAnswer` in TypeScript and `callback_queries_answer` in Python and Rust. [The interactive reference](reference/interactive.md#operation-callback-queries-answer) gives their full signatures.

| Answer setting | Rule |
| --- | --- |
| `text` | You can return up to 200 UTF-16 units. |
| `show_alert` | Set it to true for an alert instead of a toast. |
| `cache_seconds` | Use 0 to 3,600 seconds. |
| `url` | You can navigate to your own bot with a valid [callback link](deep-links.md#callback-links). |

The user's app waits up to 15 seconds for your answer. Answer within that time to show text or navigate. The API accepts one answer for up to 15 minutes after the press, but a later answer cannot update the user's screen. An expired query returns HTTP 410 `query_expired`. A second answer with another key returns HTTP 409 `query_already_answered`; replaying the original key and body returns its result.

## Edit or remove inline buttons <a href="#edit-or-remove-inline-buttons" id="edit-or-remove-inline-buttons"></a>

Use `messages.edit` with the last returned `revision` as `expected_revision`. A keyboard-only edit needs no `content`. This HTTP body removes the buttons from a message at revision `1`:

```json
{
  "expected_revision": "1",
  "reply_markup": {"type":"inline_keyboard","rows":[]}
}
```

An empty `rows` array removes inline buttons. Omitting `reply_markup` keeps them. A stale revision returns `revision_conflict`; read the current state before deciding whether your action still applies.

`open_reply` opens the reply input when the keyboard first appears. When you replace an inline keyboard through `messages.edit` or `messages.stop_poll`, omit `open_reply` to keep its value or send the same value the original keyboard used. These operations reject a changed value and accept inline keyboard replacements only.

## Reply keyboards <a href="#reply-keyboards" id="reply-keyboards"></a>

To offer conversation inputs over HTTP, send a new message with this reply keyboard body:

```json
{
  "content": {"type":"text","text":"What would you like to share?"},
  "reply_markup": {
    "type": "reply_keyboard",
    "resize": true,
    "one_time": true,
    "placeholder": "Choose an input",
    "rows": [[
      {"type":"request_location","text":"Share location"},
      {"type":"request_contact","text":"Share my ARX card"}
    ]]
  }
}
```

| Button type | Result |
| --- | --- |
| `text` | The label arrives as a message. |
| `request_users` | A selection arrives in a `users_shared` service message. |
| `request_contact` | The user chooses to share their ARX card. |
| `request_location` | The user shares a location message. |
| `request_poll` | The user creates a regular poll or quiz. |

For `request_users`, provide `request_id` with 1 to 64 ASCII letters, digits, underscores or hyphens. `max_quantity` can be 1 to 10. You can filter with `user_is_bot` and `user_is_premium`, and request a handle or photo with `request_username` and `request_photo`. Selecting a user does not grant access to other profile data.

You can restrict `request_poll.poll_type` to `regular` or `quiz`. Read the selected poll as a message, as described in [Polls and checklists](polls-and-checklists.md).

| Reply keyboard setting | Behavior |
| --- | --- |
| `persistent` | Keeps the keyboard visible. |
| `resize` | Fits its height to its buttons. |
| `one_time` | Hides it after a press; the user can show it again. |
| `placeholder` | Sets input text with 1 to 64 UTF-16 units. |

Send `{"type":"remove_keyboard"}` as `reply_markup` to remove a reply keyboard. To open the reply input for the next step, send `{"type":"force_reply","placeholder":"Type your answer"}`. Its `placeholder` is optional and accepts 1 to 64 UTF-16 units. Reply keyboards require at least one row.

For a complete interaction, open Menus and Buttons in [TypeScript](https://github.com/unusnullus/arx-sdks/tree/main/examples/typescript/23%20-%20Menus%20and%20Buttons), [Python](https://github.com/unusnullus/arx-sdks/tree/main/examples/python/23%20-%20Menus%20and%20Buttons) or [Rust](https://github.com/unusnullus/arx-sdks/tree/main/examples/rust/23%20-%20Menus%20and%20Buttons).

Continue with [Locations and contacts](locations-and-contacts.md) for consent and contact fields, or [Commands & profile](commands-and-profile.md) to configure your bot's command menu.
