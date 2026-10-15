---
description: "Reuse messages, choose attribution and handle skipped source messages."
---

# Copy and forward messages

You can copy a message without its origin, or forward it with attribution. Your bot must be able to read the source chat and write to the destination. The response tells you which messages were created and which source IDs were skipped.

## Copy a message <a href="#copy-a-message" id="copy-a-message"></a>

Call `messages.copy` with a destination chat, `from_chat_id` and a nonempty `message_ids` array. This example copies one message and protects the new copy.

Set `ARX_BOT_TOKEN`, `CHAT_ID` and `REQUEST_KEY` before running the example. Use a private chat that the user has started. See [Making requests](making-requests.md) for choosing keys and retrying requests. Replace `from_chat_id` and message ID `7` below with a readable source chat and one of its messages.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
if (!token || !key || !chatId) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const result = await bot.messagesCopy(chatId, {
		key,
		body: {
			from_chat_id: "4398046511200",
			message_ids: ["7"],
			protect_content: true,
			silent: true,
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
from arx_nexo import Client, Config, models

chat_id = os.environ["CHAT_ID"]
request = models.CopyMessages.model_validate(
    {
        "from_chat_id": "4398046511200",
        "message_ids": ["7"],
        "protect_content": True,
        "silent": True,
    }
)
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    result = bot.messages_copy(
        chat_id,
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
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
    let chat_id = std::env::var("CHAT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let request: models::CopyMessages = serde_json::from_value(serde_json::json!({
      "from_chat_id": "4398046511200",
      "message_ids": [
        "7"
      ],
      "protect_content": true,
      "silent": true
    }))?;
    let result = bot
        .messages_copy(&chat_id, &[], Some(&request), Some(&key))
        .await?;
    println!("{}", serde_json::to_string(&result)?);
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The SDKs return a batch directly; HTTP returns it under `result`. Read the destination messages in `messages` and the excluded source IDs in `skipped`.

## Choose copy or forward <a href="#choose-copy-or-forward" id="choose-copy-or-forward"></a>

| Behavior | `messages.copy` | `messages.forward` |
| --- | --- | --- |
| Origin attribution | Removes the origin and source link. | Keeps attribution, hiding private origins where required. |
| Source IDs | Accepts 1 to 100 distinct IDs in strictly increasing order. | Accepts 1 to 100 distinct IDs in strictly increasing order. |
| Albums | Keeps album grouping. | Keeps album grouping. |
| Caption replacement | Supports one source message at a time. | Keeps the original caption. |
| Replacement keyboard | Supports one source message at a time. | Does not accept a replacement keyboard. |
| Source keyboard | Does not inherit it. | Keeps an inline keyboard if every button is a URL button and its targets remain accessible. |
| `reply_to` | Can reply to a destination message or one of its parts. | Does not accept `reply_to`. |
| `protect_content` | Protects the new copy. | Protects the new forward. |
| `silent` | Sends without a notification sound. | Sends without a notification sound. |
| `effect_id` | Applies an effect in a private destination. | Applies an effect in a private destination. |
| `video_start_seconds` | Overrides the playback start. | Overrides the playback start. |

To forward, use `messagesForward` in TypeScript or `messages_forward` in Python and Rust, with a `ForwardMessages` body. The minimal body is the same source chat and ID array. Use [the message reference](reference/messages.md#operation-messages-forward) for full signatures.

## Handle skipped messages <a href="#handle-skipped-messages" id="handle-skipped-messages"></a>

Both operations skip missing or protected messages, service messages, paid media, invoices and giveaways. Always check `skipped`, even if the request succeeds.

A human sender who has neither authored nor answered an open quiz cannot copy or forward it. This restriction does not apply to your bot.

If nothing can be delivered, the API returns `protected_content` when a protected source caused the exclusion, or `message_not_found` otherwise. Validation errors reject the request before any destination messages are created. See [Limits](limits.md) for batch size and send allowances.

For an uncertain batch result, follow the retry rules in [Making requests](making-requests.md).

## Replace or remove a caption <a href="#replace-or-remove-a-caption" id="replace-or-remove-a-caption"></a>

| Copy field | Rule |
| --- | --- |
| `caption` | With one source ID, omit it to keep the caption or send an empty string to remove its text. |
| `caption_format` or `caption_entities` | Choose one and include `caption`. |
| `remove_caption: true` | Removes caption text and entities. The message keeps `fallback_text` for its media type. Do not combine it with `caption`. |
| `caption_above_media` | Changes caption position for photos, videos, animations and live photos. |
| `reply_markup` | Replaces the keyboard for one source message. Omitting it does not copy the source keyboard. |

The shared caption length and formatting rules live in [Formatting](formatting.md#choose-the-formatting-fields). Copies and forwards preserve the source's `fallback_text` unless you replace or remove a caption. ARX then updates it to match the new caption or media type. Copying leaves the original caption unchanged.

For example, copy one video with this body:

```json
{
  "from_chat_id": "4398046511200",
  "message_ids": ["7"],
  "caption": "<b>Trail preview</b>",
  "caption_format": "html",
  "caption_above_media": true,
  "video_start_seconds": 5
}
```

## Reply from a copy <a href="#reply-from-a-copy" id="reply-from-a-copy"></a>

Set `reply_to.message_id` to a message in the destination chat. You can also supply `chat_id` for an accessible reply target in another chat, `allow_missing` to allow a missing target, or `quote` to select quoted text. A copy can select a `poll_option_id` or `checklist_task_id`; see [part replies](polls-and-checklists.md#reply-to-an-option-or-task).

```json
{
  "from_chat_id": "4398046511200",
  "message_ids": ["7"],
  "reply_to": {"message_id":"12"},
  "silent": true,
  "protect_content": true
}
```

`protect_content` protects the destination message; it cannot authorize copying a protected source. `silent` changes the notification behavior, not who can read the message.

## Playback and effects <a href="#playback-and-effects" id="playback-and-effects"></a>

`video_start_seconds` must be finite, nonnegative and below every copied or forwarded video's duration. Omitting it keeps the source setting. It leaves non-video messages unchanged. Invalid playback options return HTTP 400 `invalid_parameter` for the whole batch.

Select `effect_id` from [Message effects](message-effects.md). For polls, checklists and live locations, read their [copy behavior](polls-and-checklists.md#receive-votes-and-results) and [location behavior](locations-and-contacts.md#update-or-stop-a-live-location) before treating a copy as the same interactive object.
