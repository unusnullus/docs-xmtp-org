---
description: "Show a reply while you generate it, handle Stop and publish the final message."
---

# Drafts and chat actions

You can show a temporary preview while your bot prepares a reply, then publish the finished text as a message. Drafts stay out of message history and expire 30 seconds after their last update. Use them in a private chat.

## Create and replace a draft <a href="#create-and-replace-a-draft" id="create-and-replace-a-draft"></a>

Call `drafts.set` with a `draft_id` that identifies this generation. Reuse that ID for each replacement, and use a different idempotency key for each changed chunk.

Set `ARX_BOT_TOKEN`, `CHAT_ID` and `REQUEST_KEY` before running the example. Set `DRAFT_ID` to a new ID for this generation, and keep it for later chunks. Use a private chat that the user has started. See [Making requests](making-requests.md) for choosing keys and retrying requests.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
const draftId = process.env.DRAFT_ID;
if (!token || !key || !chatId || !draftId) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID, DRAFT_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const result = await bot.draftsSet(chatId, draftId, {
		key,
		body: {
			text: "Checking the available options",
			can_stop: true,
			keep_on_stop: true,
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
draft_id = os.environ["DRAFT_ID"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.SetDraft(
        text="Checking the available options", can_stop=True, keep_on_stop=True
    )
    result = bot.drafts_set(
        chat_id,
        draft_id,
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
    let draft_id = std::env::var("DRAFT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let request = models::SetDraft {
        can_stop: Some(true),
        keep_on_stop: Some(true),
        text: Some("Checking the available options".into()),
        ..Default::default()
    };
    let result = bot
        .drafts_set(&chat_id, &draft_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

| Field or action | Rule |
| --- | --- |
| `draft_id` | Use 1 to 128 ASCII letters, digits, underscores or hyphens. |
| `text` or `rich` | Provide exactly one. |
| `text` | Use 0 to 4,096 UTF-16 units. Empty text shows a thinking indicator. |
| `format` or `entities` | Choose one for a text draft; see [Formatting](formatting.md). |
| `rich` | Provide blocks, HTML or Markdown with the usual [rich limits](formatting.md#rich-messages). Do not add a `content` wrapper. |
| Media | Reference existing `file_id` values. Drafts cannot import URLs or upload attachments. |
| Update rate | You can make five content changes per second per chat. |

The same draft ID replaces the preview. A different ID replaces the previous preview without continuing its animation. Refresh before the 30-second expiry if you still need to show progress.

Within draft rich content, you can include a `thinking` block with `text`. Remove it before sending the final rich message.

## Handle Stop <a href="#handle-stop" id="handle-stop"></a>

Set `can_stop: true` to show Stop. It defaults to false. Your bot receives a `stopped_message_generation` update when the user presses it. This update type belongs to the default filter; if you use an explicit list, include it there. See [update filters](updates.md#filters).

Read `stopped_message_generation.chat`, `draft_id` and `user`. Cancel the matching generation and stop publishing its chunks. Repeated presses create one update, but delivery can repeat, so cancellation should be safe to run again.

| `keep_on_stop` | What the user sees |
| --- | --- |
| false (default) | The draft disappears. |
| true | The current preview remains until its existing expiry, with the Stop button removed. |

Stop does not save partial text in history. If you want to retain that text, send it explicitly with `messages.send`.

## Finish or remove the preview <a href="#finish-or-remove-the-preview" id="finish-or-remove-the-preview"></a>

Send the finished reply with `messages.send`. A newly accepted message from your bot retires the current draft. Replaying an accepted send, editing a message or receiving a user's message does not retire it.

To remove a preview without sending a final reply, call `drafts.delete` with the same chat and draft IDs. The typed methods are `draftsDelete` in TypeScript and `drafts_delete` in Python and Rust; they return true.

## Retry a draft write <a href="#retry-a-draft-write" id="retry-a-draft-write"></a>

Retry the same chunk with the same key and body if you did not receive its result. A replay returns the accepted result without extending expiry or using another content change allowance. Changing a request while reusing its key returns HTTP 409 `idempotency_key_reused`.

Stop or a later bot message retires the draft ID. Further writes to that ID return HTTP 409 `version_conflict` with `reason: draft_retired`. Start a new generation with a fresh ID. Deleting the final message does not make the retired ID reusable.

## Show a chat action <a href="#show-a-chat-action" id="show-a-chat-action"></a>

Use `chats.send_action` when a short activity indicator is enough. Send, for example, `{"action":"typing"}`. The indicator lasts five seconds or until your bot sends a message. Calls less than one second apart combine into the same activity.

[The draft reference](reference/drafts.md) lists all action values and the typed methods. For a complete bot that uses temporary replies, open Streaming Replies in [TypeScript](https://github.com/unusnullus/arx-sdks/tree/main/examples/typescript/26%20-%20Streaming%20Replies), [Python](https://github.com/unusnullus/arx-sdks/tree/main/examples/python/26%20-%20Streaming%20Replies) or [Rust](https://github.com/unusnullus/arx-sdks/tree/main/examples/rust/26%20-%20Streaming%20Replies).
