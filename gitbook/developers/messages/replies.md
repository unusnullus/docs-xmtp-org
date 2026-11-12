---
description: "Reply to a message, quote an exact excerpt, and handle reply targets that no longer exist."
---

# Replies and quotes

You can make any new message a reply by adding `reply_to` beside `content`. A reply can quote an exact excerpt of the target, point at one poll option or checklist task, or target a message in another private chat of your bot.

## Reply with a quote <a href="#reply-with-a-quote" id="reply-with-a-quote"></a>

This example replies to a text message and quotes the word `Friday` from it.

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REPLY_TO_MESSAGE_ID` and a new `REQUEST_KEY`. The target must be a text message that contains `Friday`. See [Making requests](../making-requests.md) for choosing keys and retrying requests.

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
			content: { type: "text", text: "Friday works. I booked the room." },
			reply_to: { message_id: replyTo, quote: { text: "Friday" } },
		},
	});
	console.log(message.message_id);
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
        content=FormattedText("Friday works. I booked the room.").content(),
        reply_to=models.InputReply(
            message_id=os.environ["REPLY_TO_MESSAGE_ID"],
            quote=models.InputQuote(text="Friday"),
        ),
    )
    message = bot.messages_send(
        os.environ["CHAT_ID"],
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
    print(message.message_id)
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
    let text = FormattedText::new("Friday works. I booked the room.", vec![])?;
    let reply = models::InputReply {
        quote: Some(models::InputQuote::new("Friday".into())),
        ..models::InputReply::new(reply_to)
    };
    let request = models::SendMessage {
        reply_to: Some(reply),
        ..models::SendMessage::new(text.content()?)
    };
    let message = bot
        .messages_send(&chat_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{}", message.message_id());
    Ok(())
}
```

{% endtab %}
{% endtabs %}

For a reply in the same chat, the accepted message contains `reply_to.message` with a snapshot of the target as ARX accepted it, plus the resolved quote with its `position`. For a reply to another chat, `reply_to.external` gives only the content type and a hidden origin.

## Reply fields <a href="#reply-fields" id="reply-fields"></a>

| Field | Rule |
| --- | --- |
| `message_id` | Required. The message you reply to. |
| `chat_id` | Optional. Another private chat of your bot that contains the target. Omit it for the current chat. |
| `allow_missing` | Optional. Set it to true to send the message without a reply when the target no longer exists. It works only within the current chat. |
| `quote` | Optional. An exact excerpt of the target. See below. |
| `poll_option_id` or `checklist_task_id` | Optional. Replies to one poll option or checklist task. Choose at most one. See [Polls and checklists](../polls-and-checklists.md#reply-to-an-option-or-task). |

`messages.send`, `messages.send_album` and `messages.copy` accept `reply_to`. `messages.forward` does not.

## Quote rules <a href="#quote-rules" id="quote-rules"></a>

| Quote field | Rule |
| --- | --- |
| `text` | Required. Up to 1,024 UTF-16 units. It must match the target's text exactly. An empty `text` sends the reply without a quote. |
| `position` | Optional. The UTF-16 offset of the excerpt in the target's text. Omit it to use the first match. |
| `format` or `entities` | Optional. When you send formatting, it must match the target's own formatting over the excerpt. Quotes keep `bold`, `italic`, `underline`, `strikethrough`, `spoiler`, `custom_emoji` and `date_time`; a `date_time` or `custom_emoji` span must be quoted whole. |

You can quote a text message, or the text of the poll option or checklist task you select. A quote that does not match returns HTTP 400 `quote_not_found`. Count offsets as described in [Formatting](../formatting.md#utf-16-offsets).

## Missing and unsupported targets <a href="#missing-and-unsupported-targets" id="missing-and-unsupported-targets"></a>

| Situation | Result |
| --- | --- |
| The target does not exist and `allow_missing` is not set | HTTP 404 `message_not_found`, with `field: reply_to`. |
| The target does not exist and `allow_missing` is true | The message is sent without a reply. |
| `allow_missing` with a target in another chat | HTTP 400 `invalid_request`. |
| `chat_id` names a chat that is not your bot's | HTTP 404 `chat_not_found`. |
| A quote or a reply across chats when your message or the target is `rich` | HTTP 400 `unsupported_content`. |

## In groups and topics <a href="#in-groups-and-topics" id="in-groups-and-topics"></a>

In a Super Group, `reply_to` accepts `message_id`, `allow_missing` and a `chat_id` equal to the group itself. Quotes, part replies and replies to other chats return HTTP 403 `capability_not_available`. A reply to a message in a topic is sent into that topic; a `topic_id` that names another topic is rejected. See [Groups](../groups.md).

Continue with [Send messages](../messages.md) for the other request fields.
