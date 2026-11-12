---
description: "React to messages with an allowed emoji, read users' reactions, and pin or unpin messages."
---

# Reactions and pins

Your bot can react to a message with one emoji and pin messages at the top of a chat. Use `reactions.set` for reactions and `messages.pin`, `messages.unpin` and `messages.unpin_all` for pins. All four operations return `true`.

## React to a message <a href="#react-to-a-message" id="react-to-a-message"></a>

Choose an emoji from the platform catalog, then set it on the message. This example reads the catalog with `platform.get` and reacts with its first allowed emoji.

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `MESSAGE_ID` and a new `REQUEST_KEY`. See [Making requests](../making-requests.md) for choosing keys and retrying requests.

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
	const platform = await bot.platformGet();
	const emoji = platform.reactions.allowed_emoji[0];
	if (!emoji) throw new Error("No reaction is available");
	const result = await bot.reactionsSet(chatId, messageId, {
		key,
		body: { reactions: [{ type: "emoji", emoji }] },
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
    platform = bot.platform_get()
    if not platform.reactions.allowed_emoji:
        raise RuntimeError("No reaction is available")
    emoji = platform.reactions.allowed_emoji[0]
    result = bot.reactions_set(
        os.environ["CHAT_ID"],
        os.environ["MESSAGE_ID"],
        body=models.SetReactions(reactions=[models.Reaction(type="emoji", emoji=emoji)]),
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
    let message_id = std::env::var("MESSAGE_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let platform = bot.platform_get(&query, None, None).await?;
    let emoji = platform
        .reactions
        .allowed_emoji
        .first()
        .ok_or("No reaction is available")?
        .clone();
    let request = models::SetReactions::new(vec![models::Reaction::new(emoji)]);
    let result = bot
        .reactions_set(&chat_id, &message_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

## Reaction rules <a href="#reaction-rules" id="reaction-rules"></a>

| Field | Rule |
| --- | --- |
| `reactions` | Required. An array with at most one `{"type":"emoji","emoji":...}` item. The emoji must be in `platform.get` `reactions.allowed_emoji`. An empty array removes your bot's reaction. |
| `is_big` | Optional. Set it to true to play the reaction with a larger animation. |

Each call replaces your bot's previous reaction on that message. Repeating the current reaction changes nothing. When you react to an album item, the reaction goes to the first item of the album that is still visible.

| Error | Cause |
| --- | --- |
| HTTP 400 `reaction_not_allowed` | More than one reaction, an emoji outside the catalog, or a reaction on the chat creation message. |
| HTTP 403 `capability_not_available` | A `custom_emoji` reaction. Custom emoji are not available to bots. |
| HTTP 404 `message_not_found` | The message does not exist in this chat. |

Do not hardcode emoji. Read `platform.get` when your bot starts and when you see `reaction_not_allowed`.

## Receive reactions <a href="#receive-reactions" id="receive-reactions"></a>

Users' reactions arrive as `message_reaction` updates when you opt in to them. See [update filters](../updates.md#filters). Each update carries:

| Field | Meaning |
| --- | --- |
| `chat` | The chat that contains the message. |
| `message_id` | The message that received the reaction. |
| `user` | The user who changed the reaction. |
| `old_reactions`, `new_reactions` | The user's reactions before and after the change. An empty `new_reactions` means the user removed the reaction. |
| `at` | When the change happened. |

The `reactions` field of a returned message holds `counts`, your bot's `my_reactions` and `is_big`.

## Pin a message <a href="#pin-a-message" id="pin-a-message"></a>

Call `messages.pin` with the chat and message IDs. Set `silent: true` to pin without a notification sound.

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
	const result = await bot.messagesPin(chatId, messageId, {
		key,
		body: { silent: true },
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
    result = bot.messages_pin(
        os.environ["CHAT_ID"],
        os.environ["MESSAGE_ID"],
        body=models.PinMessage(silent=True),
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
    let message_id = std::env::var("MESSAGE_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let request = models::PinMessage {
        silent: Some(true),
        ..Default::default()
    };
    let result = bot
        .messages_pin(&chat_id, &message_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

Pinning adds a `message_pinned` service message to the chat, which points to the pinned message. Pinning a message that is already pinned changes nothing.

## Unpin messages <a href="#unpin-messages" id="unpin-messages"></a>

| Operation | Body | Effect |
| --- | --- | --- |
| `messages.unpin` | `{"message_id":"42"}` | Unpins that message. |
| `messages.unpin` | `{}` | Unpins the most recently pinned message. |
| `messages.unpin_all` | `{}` | Unpins every message in the chat. |

The typed methods are `messagesUnpin` and `messagesUnpinAll` in TypeScript, and `messages_unpin` and `messages_unpin_all` in Python and Rust.

## Pin rules <a href="#pin-rules" id="pin-rules"></a>

| Rule | Value |
| --- | --- |
| Pinned messages per private chat | 1,000. Pinning another returns HTTP 409 `pin_limit_reached`; unpin a message first. |
| Retries | Repeat the same key and body to recover an uncertain result. |

## In groups and topics <a href="#in-groups-and-topics" id="in-groups-and-topics"></a>

In a Super Group, your bot needs the administrator right to pin messages before it can pin or unpin. Your bot still sets at most one reaction, and the group's reaction settings apply. Topics have their own unpin operation. See [Groups](../groups.md).

Continue with the [interactive reference](../reference/interactive.md#operation-reactions-set) for `reactions.set`, or the [message reference](../reference/messages.md#operation-messages-pin) for the pin operations.
