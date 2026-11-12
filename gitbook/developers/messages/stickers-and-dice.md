---
description: "Send static, animated and video stickers, and dice messages with a result that ARX generates."
---

# Stickers and dice

You can send a sticker with `content.type: sticker` and an animated dice with `content.type: dice`. Both go through `messages.send`. Neither takes a caption.

## Send a sticker <a href="#send-a-sticker" id="send-a-sticker"></a>

This example uploads a new sticker file in a multipart request and associates an emoji with it.

Set `ARX_BOT_TOKEN`, `CHAT_ID` and `REQUEST_KEY`, and set `STICKER_PATH` to a sticker file that follows the formats below. Use a private chat that the user has started. See [Making requests](../making-requests.md) for choosing keys and retrying requests.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, FileInput, UploadSource } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
const path = process.env.STICKER_PATH;
if (!token || !key || !chatId || !path) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID, STICKER_PATH");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const message = await bot.messagesSendMultipart(chatId, {
		key,
		body: {
			content: {
				type: "sticker",
				sticker: FileInput.fromAttachment("sticker"),
				emoji: "👍",
			},
		},
		files: { sticker: UploadSource.fromPath(path) },
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
from arx_nexo import Client, Config, FileInput, UploadSource, models

with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.SendMessage(
        content=models.InputContent(
            root=models.InputStickerContent(
                type="sticker",
                sticker=FileInput.from_attachment("sticker"),
                emoji="👍",
            )
        )
    )
    message = bot.messages_send_multipart(
        os.environ["CHAT_ID"],
        body=request,
        files={"sticker": UploadSource.from_path(os.environ["STICKER_PATH"])},
        key=os.environ["REQUEST_KEY"],
    )
    print(message.message_id)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, FileInput, UploadPart, UploadSource, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let path = std::env::var("STICKER_PATH")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let sticker = models::InputStickerContent {
        emoji: Some("👍".into()),
        ..models::InputStickerContent::new(FileInput::from_attachment("sticker")?)
    };
    let request =
        models::SendMessage::new(models::InputContent::InputStickerContent(Box::new(sticker)));
    let files = vec![UploadPart::new("sticker", UploadSource::from_path(path)?)?];
    let message = bot
        .messages_send_multipart(&chat_id, &request, files, Some(&key))
        .await?;
    println!("{}", message.message_id());
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The response contains `content.sticker` with the sticker's `file_id`, `width`, `height`, `is_animated` and `is_video`. Reuse that `file_id` to send the same sticker again without uploading it. You can also upload a sticker to your file library with `files.upload` and `kind: sticker`.

## Sticker formats <a href="#sticker-formats" id="sticker-formats"></a>

ARX inspects the file and sets the sticker type from its contents.

| Sticker | Format | Maximum size | Other rules |
| --- | --- | --- | --- |
| Static | WebP without animation. | 512 KiB (524,288 bytes). | One side is exactly 512 pixels and the other is at most 512. |
| Animated | TGS (gzip-compressed Lottie). | 64 KiB compressed and 2 MiB uncompressed. | 512 by 512 pixels, 60 frames per second, at most 180 frames (3 seconds). Expressions, external assets, masks, effects, 3D layers and text layers are rejected. |
| Video | WebM with one VP9 video stream and no audio. | 256 KiB (262,144 bytes). | One side is exactly 512 pixels and the other is at most 512. At most 3 seconds and 30 frames per second. |

A file that fails these checks is rejected before the message is sent. A file over its size limit returns HTTP 413 `file_too_large`; see the [file error catalog](../errors.md#files-and-uploads) for the other file errors.

## Sticker rules <a href="#sticker-rules" id="sticker-rules"></a>

| Source | Behavior |
| --- | --- |
| `attach` | Uploads a new sticker. You can set `emoji` to one emoji, at most 64 UTF-8 bytes; an invalid value returns HTTP 400 before anything is stored. |
| `file_id` | Reuses a sticker your bot can access. `emoji` is ignored; the original association is kept. |
| `url` | Imports a static WebP sticker of up to 512 KiB from a public URL. `emoji` is ignored. |

Stickers have no caption, cannot be album items, and cannot be replaced with `messages.edit`.

## Send a dice <a href="#send-a-dice" id="send-a-dice"></a>

A dice message shows an animated emoji. ARX picks the result when it accepts the message, and you read it from the response. This example throws a dart.

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
	const message = await bot.messagesSend(chatId, {
		key,
		body: { content: { type: "dice", emoji: "🎯" } },
	});
	if (message.kind === "content" && message.content.type === "dice") {
		console.log(message.content.dice.value);
	}
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
    request = models.SendMessage(
        content=models.InputContent(root=models.InputDice(type="dice", emoji="🎯"))
    )
    message = bot.messages_send(
        os.environ["CHAT_ID"],
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
    print(message.content)
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
    let query = [];
    let dice = models::InputDice {
        emoji: Some(models::InputDiceEmoji::Value2),
        ..Default::default()
    };
    let request = models::SendMessage::new(models::InputContent::InputDice(Box::new(dice)));
    let message = bot
        .messages_send(&chat_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{message:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

Omit `emoji` to throw the default die. `platform.get` lists the current dice emoji in `dice_emoji`.

| Emoji | Result range |
| --- | --- |
| 🎲 (default) | 1 to 6 |
| 🎯 | 1 to 6 |
| 🎳 | 1 to 6 |
| 🏀 | 1 to 5 |
| ⚽ | 1 to 5 |
| 🎰 | 1 to 64 |

The result is in `content.dice.value` of the returned message. Your bot cannot choose it.

## Dice rules <a href="#dice-rules" id="dice-rules"></a>

- A dice message cannot be edited. `messages.edit` returns HTTP 400 `message_not_editable`.
- Your bot cannot delete a dice message during the first 24 hours after it was sent. The request returns HTTP 403 `message_cant_be_deleted` with `reason: dice_too_recent`.

## In groups and topics <a href="#in-groups-and-topics" id="in-groups-and-topics"></a>

Super Groups accept stickers through `messages.send`, without a keyboard. They do not accept dice. See [Groups](../groups.md).

Continue with [Send messages](../messages.md) for the other content types, or the [message reference](../reference/messages.md#operation-messages-send) for the full schema.
