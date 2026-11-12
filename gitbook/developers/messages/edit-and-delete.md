---
description: "Change your bot's messages with revision checks, and delete messages one at a time or in batches."
---

# Edit and delete messages

You can change a message your bot sent with `messages.edit`, and remove messages with `messages.delete` or `messages.delete_batch`. Every message carries a `revision`. Pass it back as `expected_revision` so an edit never overwrites a change you have not seen.

## Edit a message <a href="#edit-a-message" id="edit-a-message"></a>

This example replaces the text of a message and its inline keyboard. Use the `revision` returned by the send or by your last edit.

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `MESSAGE_ID`, `REVISION` and a new `REQUEST_KEY`. See [Making requests](../making-requests.md) for choosing keys and retrying requests.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, InlineButton, InlineKeyboard } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
const messageId = process.env.MESSAGE_ID;
const revision = process.env.REVISION;
if (!token || !key || !chatId || !messageId || !revision) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID, MESSAGE_ID, REVISION");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const message = await bot.messagesEdit(chatId, messageId, {
		key,
		body: {
			expected_revision: revision,
			content: { type: "text", text: "Order shipped. Tracking is ready." },
			reply_markup: new InlineKeyboard()
				.row(InlineButton.url("Track order", "https://example.com/track/42"))
				.build(),
		},
	});
	console.log(message.revision);
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, InlineButton, InlineKeyboard, models

with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.EditMessage(
        expected_revision=os.environ["REVISION"],
        content=models.InputEditContent(
            root=models.InputText(type="text", text="Order shipped. Tracking is ready.")
        ),
        reply_markup=InlineKeyboard()
        .row(InlineButton.url("Track order", "https://example.com/track/42"))
        .build(),
    )
    message = bot.messages_edit(
        os.environ["CHAT_ID"],
        os.environ["MESSAGE_ID"],
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
    print(message.revision)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, InlineButton, InlineKeyboard, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let message_id = std::env::var("MESSAGE_ID")?;
    let revision = std::env::var("REVISION")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let keyboard = InlineKeyboard::new().row(vec![InlineButton::url(
        "Track order",
        "https://example.com/track/42",
        None,
    )?])?;
    let text = models::InputText::new("Order shipped. Tracking is ready.".into());
    let request = models::EditMessage {
        expected_revision: Some(revision),
        content: Some(models::InputEditContent::InputText(Box::new(text))),
        reply_markup: Some(keyboard.build()?),
        ..Default::default()
    };
    let message = bot
        .messages_edit(&chat_id, &message_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{:?}", message.revision());
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The response is the edited message. A change increments `revision` once and sets `edited_at`. An edit that changes nothing keeps the revision. If the message changed since the revision you sent, the edit returns HTTP 409 `revision_conflict`; read the current state before you decide whether your change still applies.

## What you can change <a href="#what-you-can-change" id="what-you-can-change"></a>

An edit request makes one content change, optionally together with a keyboard change.

| Change | Fields | Rule |
| --- | --- | --- |
| Replace the content | `content` | The current and the new content must both be one of `text`, `rich`, `photo`, `video`, `animation`, `audio`, `document`, `live_photo` or `checklist`. Add `link_preview` to change a text message's preview. |
| Change a caption | `caption`, `caption_format` or `caption_entities`, `caption_above_media` | Keeps the media. Omit `caption` to keep the text. Change only `caption_above_media` to move the caption and keep its formatting. An empty caption removes the text. |
| Move a live location | `live_location` | See [Locations and contacts](../locations-and-contacts.md#update-or-stop-a-live-location). |
| Replace or remove the inline keyboard | `reply_markup` | Send an inline keyboard. Empty `rows` removes the buttons; omitting `reply_markup` keeps them. See [Keyboards](../keyboards.md#edit-or-remove-inline-buttons). |

Do not combine `content`, caption fields and `live_location` in one request. A request with none of them and no `reply_markup` is invalid. Caption limits and formatting follow [Formatting](../formatting.md#choose-the-formatting-fields).

To replace media with a new file, send the edit as multipart with `messagesEditMultipart` in TypeScript or `messages_edit_multipart` in Python and Rust, and reference the part with `attach`. To reuse a file, pass its `file_id`. In an album, the new content must stay in the item's family; see [Albums](albums.md#work-with-album-items).

## What you cannot edit <a href="#what-you-cannot-edit" id="what-you-cannot-edit"></a>

| Message | Result |
| --- | --- |
| A message another user or bot sent | HTTP 400 `message_not_editable`, reason `not_message_author`. |
| A service message | `message_not_editable`, reason `service_message`. |
| A message sent with a reply keyboard | `message_not_editable`, reason `reply_keyboard`. |
| A poll | `message_not_editable`, reason `poll_content`. Close it with `messages.stop_poll` instead. See [Polls and checklists](../polls-and-checklists.md#close-a-poll-and-change-its-options). |
| `voice`, `video_note`, `sticker`, `dice`, `location`, `venue`, `contact` content | `message_not_editable`. Live locations change through `live_location` only. |

Your bot has no time limit for editing its own messages in a private chat.

## Delete a message <a href="#delete-a-message" id="delete-a-message"></a>

Call `messages.delete` with the chat and message IDs. It returns `true`. This example deletes one message.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
const messageId = process.env.MESSAGE_ID;
if (!token || !key || !chatId || !messageId) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID, MESSAGE_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	console.log(await bot.messagesDelete(chatId, messageId, { key }));
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    result = bot.messages_delete(
        os.environ["CHAT_ID"],
        os.environ["MESSAGE_ID"],
        key=os.environ["REQUEST_KEY"],
    )
    print(result)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let message_id = std::env::var("MESSAGE_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot
        .messages_delete(&chat_id, &message_id, &query, None, Some(&key))
        .await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

## Delete several messages <a href="#delete-several-messages" id="delete-several-messages"></a>

`messages.delete_batch` takes `message_ids`: 1 to 100 distinct IDs from the same chat. For HTTP:

```json
{"message_ids":["41","42","43"]}
```

The typed methods are `messagesDeleteBatch` in TypeScript and `messages_delete_batch` in Python and Rust. The result lists `deleted` and `skipped` IDs. A batch does not fail because one message is missing or blocked by a rule below; that ID appears in `skipped` instead.

## Deletion rules in private chats <a href="#deletion-rules-in-private-chats" id="deletion-rules-in-private-chats"></a>

| Message | Rule |
| --- | --- |
| Your bot's own message | You can delete it at any time. |
| A message the user sent | You can delete it within 48 hours. Later, `messages.delete` returns HTTP 403 `message_too_old`. |
| A dice message | You cannot delete it during its first 24 hours. The request returns HTTP 403 `message_cant_be_deleted` with `reason: dice_too_recent`. |
| The chat creation or topic creation service message | You cannot delete it. The request returns `message_cant_be_deleted` with `reason: creation_service`. |

A missing ID returns HTTP 404 `message_not_found` from `messages.delete`. To learn when users delete messages, opt in to the `messages_deleted` update; see [update filters](../updates.md#filters).

## Retry an edit or delete <a href="#retry-an-edit-or-delete" id="retry-an-edit-or-delete"></a>

Retry with the same `Idempotency-Key` and body when you did not receive a result. The replay returns the original result, even if the message changed afterwards. Use a new key for a new change.

## In groups and topics <a href="#in-groups-and-topics" id="in-groups-and-topics"></a>

In a Super Group, your bot edits its own text messages, captions and the inline keyboards of its text messages. It can delete its own messages, and other members' messages when it is an administrator with the right to delete messages. In a group, your bot cannot delete any message older than 48 hours. See [Groups](../groups.md).

Continue with the [message reference](../reference/messages.md#operation-messages-edit) for the complete request schemas.
