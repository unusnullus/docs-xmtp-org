---
description: "Discover available animations and add an effect to a private message."
---

# Message effects

You can add an animation to a private message by selecting an effect from the current catalog. Use `message_effects.list` to discover effects, then pass an accepted `effect_id` to `messages.send`, `messages.send_album`, `messages.copy` or `messages.forward`.

## Choose and send an effect <a href="#choose-and-send-an-effect" id="choose-and-send-an-effect"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID` and `REQUEST_KEY` before running the example. Use a private chat that the user has started. See [Making requests](making-requests.md) for choosing keys and retrying requests. This example selects the first effect available in private chats. If the catalog has none, it stops before sending.

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
	const catalog = await bot.messageEffectsList();
	const effect = catalog.items.find((item) =>
		item.available_in.includes("private"),
	);
	if (!effect) throw new Error("No private-chat effect is available");
	const result = await bot.messagesSend(chatId, {
		key,
		body: {
			content: { type: "text", text: "Trail saved." },
			effect_id: effect.effect_id,
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
from arx_nexo import Client, Config, models, FormattedText

chat_id = os.environ["CHAT_ID"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    catalog = bot.message_effects_list()
    effect = next(
        (item for item in catalog.items if "private" in item.available_in), None
    )
    if effect is None:
        raise RuntimeError("No private-chat effect is available")
    request = models.SendMessage(
        content=FormattedText("Trail saved.").content(),
        effect_id=effect.effect_id,
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
use arx_nexo::{Client, Config, FormattedText, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let catalog = bot.message_effects_list(&query, None, None).await?;
    let effect = catalog
        .items
        .iter()
        .find(|item| {
            item.available_in
                .iter()
                .any(|kind| matches!(kind, models::MessageEffectAvailableInItem::Private))
        })
        .ok_or("No private-chat effect is available")?;
    let request = models::SendMessage {
        effect_id: Some(effect.effect_id.clone()),
        ..models::SendMessage::new(FormattedText::new("Trail saved.", vec![])?.content()?)
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

The send returns the accepted `Message`. Keep its `message_id` if you need to edit or reply to it later. Follow [Making requests](making-requests.md) when retrying a send.

## Read the catalog <a href="#read-the-catalog" id="read-the-catalog"></a>

`message_effects.list` returns `items` with these fields:

| Field | Meaning |
| --- | --- |
| `effect_id` | The value you pass to a message operation. |
| `emoji` | An emoji that represents the effect. |
| `title` | A display title with at most 64 UTF-16 units. |
| `available_in` | The chat kinds that support the effect; the current catalog uses `private`. |
| `asset_version` | The version of the animation asset. |

Do not hardcode an effect ID or assume that a previously selected effect remains available. Reading the catalog does not send or choose an effect. If the user's app cannot display the animation, it still displays the message content.

## Handle an invalid effect <a href="#handle-an-invalid-effect" id="handle-an-invalid-effect"></a>

An unknown or retired ID returns HTTP 400 `invalid_parameter` with `reason: invalid_message_options`. Refresh the catalog and select an available effect, or send the content without `effect_id`.

Continue with [Copy and forward](copy-and-forward.md) to apply an effect while reusing a message, or [the message reference](reference/messages.md) for send options.
