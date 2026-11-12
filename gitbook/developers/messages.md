---
description: "Choose a content type, set delivery options and keep the IDs you need to change a message later."
---

# Send messages

You send every single message with `messages.send`. The `content.type` field selects what the message contains, and the fields beside `content` control replies, keyboards, notifications and protection. Keep the returned `message_id` and `revision` if you plan to edit, delete, pin or react to the message.

## Send a message <a href="#send-a-message" id="send-a-message"></a>

This example sends a silent, protected text message that replies to a message in the same chat.

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REPLY_TO_MESSAGE_ID` and `REQUEST_KEY` before running the example. Use a private chat that the user has started. See [Making requests](making-requests.md) for choosing keys and retrying requests.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
const replyTo = process.env.REPLY_TO_MESSAGE_ID;
if (!token || !key || !chatId || !replyTo) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID, REPLY_TO_MESSAGE_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const message = await bot.messagesSend(chatId, {
		key,
		body: {
			content: { type: "text", text: "Your order shipped." },
			reply_to: { message_id: replyTo },
			silent: true,
			protect_content: true,
		},
	});
	console.log(message.message_id, message.revision);
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, FormattedText, models

with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.SendMessage(
        content=FormattedText("Your order shipped.").content(),
        reply_to=models.InputReply(message_id=os.environ["REPLY_TO_MESSAGE_ID"]),
        silent=True,
        protect_content=True,
    )
    message = bot.messages_send(
        os.environ["CHAT_ID"],
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
    print(message.message_id, message.revision)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, FormattedText, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let reply_to = std::env::var("REPLY_TO_MESSAGE_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let text = FormattedText::new("Your order shipped.", vec![])?;
    let request = models::SendMessage {
        reply_to: Some(models::InputReply::new(reply_to)),
        silent: Some(true),
        protect_content: Some(true),
        ..models::SendMessage::new(text.content()?)
    };
    let message = bot
        .messages_send(&chat_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{} {:?}", message.message_id(), message.revision());
    Ok(())
}
```

{% endtab %}
{% endtabs %}

## Request fields <a href="#request-fields" id="request-fields"></a>

| Field | What it does |
| --- | --- |
| `content` | Required. The message body. `content.type` selects the variant. |
| `reply_to` | Replies to a message, optionally quoting part of it. See [Replies and quotes](messages/replies.md). |
| `reply_markup` | Attaches an inline keyboard, shows a reply keyboard, removes one or opens the reply input. See [Keyboards](keyboards.md). |
| `silent` | Delivers the message without a notification sound. |
| `protect_content` | Prevents the message from being copied or forwarded. |
| `effect_id` | Plays an animation with the message in a private chat. See [Message effects](message-effects.md). |
| `link_preview` | Controls the preview of a link in a text message. See [Formatting](formatting.md#link-previews). |
| `topic_id` | Sends the message into a topic. See [Groups](groups.md). |

Unknown fields are rejected. Send each request with an `Idempotency-Key`; a retry with the same key and body returns the original message instead of sending a second one.

## Content types <a href="#content-types" id="content-types"></a>

| `content.type` | What the user sees | Guide |
| --- | --- | --- |
| `text` | Text with optional formatting and a link preview. | [Formatting](formatting.md) |
| `rich` | A structured document with headings, lists, tables, media and buttons. | [Formatting](formatting.md#rich-messages) |
| `photo`, `video`, `animation` | Visual media with an optional caption. | [Files and media](files.md) |
| `audio`, `voice`, `document` | Audio tracks, voice messages and files. | [Files and media](files.md) |
| `video_note` | A round video message, without a caption. | [Files and media](files.md#video-notes) |
| `live_photo` | A photo paired with a short video. | [Files and media](files.md#live-photos) |
| `sticker` | A static, animated or video sticker. | [Stickers and dice](messages/stickers-and-dice.md) |
| `dice` | An animated emoji with a random result. | [Stickers and dice](messages/stickers-and-dice.md#send-a-dice) |
| `poll`, `checklist` | A poll, quiz or task list. | [Polls and checklists](polls-and-checklists.md) |
| `location`, `venue` | A static or live location, or a named place. | [Locations and contacts](locations-and-contacts.md) |
| `contact` | An ARX account card or a manual contact card. | [Locations and contacts](locations-and-contacts.md#request-an-arx-card-with-consent) |
| `invoice` | A payment request. | [Payments](payments.md) |

To send 2 to 10 photos, videos, live photos, audio files or documents as one group, use `messages.send_album` instead. See [Albums](messages/albums.md).

## Attach new files <a href="#attach-new-files" id="attach-new-files"></a>

Media content takes an `InputFile` with exactly one of `file_id`, `url` or `attach`. To upload new bytes in the same request, send a multipart body: a JSON part named `payload` with the request, and one binary part for each `attach` name. The typed multipart methods are `messagesSendMultipart` in TypeScript and `messages_send_multipart` in Python and Rust. [Files and media](files.md) lists the size and format rules for each type.

## Read the response <a href="#read-the-response" id="read-the-response"></a>

`messages.send` returns the accepted message. Keep these fields:

| Field | Use it to |
| --- | --- |
| `message_id` | Edit, delete, pin, react to or reply to the message. IDs are decimal strings. |
| `revision` | Pass as `expected_revision` when you edit the message. See [Edit and delete](messages/edit-and-delete.md). |
| `content` | Read resolved values, such as file IDs, parsed entities, poll option IDs or a dice result. |
| `fallback_text` | Show a plain-text summary of the message, for example in logs or notifications. |

## Errors to handle <a href="#errors-to-handle" id="errors-to-handle"></a>

| Error | What happened | What to do |
| --- | --- | --- |
| HTTP 403 `chat_not_started` | The user has not started your bot. | Wait for the user to open the chat. |
| HTTP 403 `bot_blocked_by_user` | The user blocked your bot. | Stop sending to this chat. |
| HTTP 400 `invalid_parameter` or `invalid_request` | A field breaks an input rule. | Read `field` and `reason` in the error and fix the request. |
| HTTP 403 `capability_not_available` | The content or option is not available in this chat. | Remove the option, or send a supported content type. |
| HTTP 409 `file_not_ready` | A referenced file is still processing. | Wait until the file is ready. See [Files and media](files.md). |
| HTTP 429 `rate_limited` | You exceeded a send rate. | Wait for `retry_after`, then retry with the same key. See [Limits](limits.md). |

The full catalog is in [Errors](errors.md).

## In groups and topics <a href="#in-groups-and-topics" id="in-groups-and-topics"></a>

In a Super Group, `messages.send` accepts a smaller set of fields and content types:

| Accepted in a Super Group | Not accepted |
| --- | --- |
| `text`, `photo`, `video`, `animation`, `audio`, `voice`, `video_note`, `document` and `sticker` content. | `rich`, `live_photo`, `dice`, `poll`, `checklist`, `location`, `venue`, `contact` and `invoice` content. |
| `silent`, `protect_content`, `reply_to` and `topic_id`. | `effect_id` and `link_preview`. |
| An inline keyboard on a text message. | Reply keyboards, and keyboards on media messages. |

An unsupported field or content type returns HTTP 403 `capability_not_available`. Albums, copies and forwards work in private chats only. Group permissions, slow mode and topics add their own rules; see [Groups](groups.md).

Continue with the [message reference](reference/messages.md#operation-messages-send) for the complete request schema.
