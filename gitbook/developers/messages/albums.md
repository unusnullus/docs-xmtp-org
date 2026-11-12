---
description: "Send 2 to 10 photos, videos, live photos, audio files or documents as one grouped message."
---

# Albums

You can send several media items as one album with `messages.send_album`. ARX creates every item or none of them. Each item becomes its own message, and all of them share a `media_group_id`.

## Send an album <a href="#send-an-album" id="send-an-album"></a>

This example uploads two photos in one multipart request and captions the first one.

Set `ARX_BOT_TOKEN`, `CHAT_ID` and `REQUEST_KEY`, and set `PHOTO_1` and `PHOTO_2` to paths of JPEG, PNG or WebP files. Use a private chat that the user has started. See [Making requests](../making-requests.md) for choosing keys and retrying requests.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, FileInput, UploadSource } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
const first = process.env.PHOTO_1;
const second = process.env.PHOTO_2;
if (!token || !key || !chatId || !first || !second) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID, PHOTO_1, PHOTO_2");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const album = await bot.messagesSendAlbumMultipart(chatId, {
		key,
		body: {
			items: [
				{
					type: "photo",
					media: FileInput.fromAttachment("first"),
					caption: "<b>Trail day</b>",
					caption_format: "html",
				},
				{ type: "photo", media: FileInput.fromAttachment("second") },
			],
		},
		files: {
			first: UploadSource.fromPath(first),
			second: UploadSource.fromPath(second),
		},
	});
	for (const message of album.messages) {
		console.log(message.message_id, message.media_group_id);
	}
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
    request = models.SendAlbum(
        items=[
            models.AlbumItem(
                root=models.AlbumPhoto(
                    type="photo",
                    media=FileInput.from_attachment("first"),
                    caption="<b>Trail day</b>",
                    caption_format="html",
                )
            ),
            models.AlbumItem(
                root=models.AlbumPhoto(
                    type="photo", media=FileInput.from_attachment("second")
                )
            ),
        ]
    )
    album = bot.messages_send_album_multipart(
        os.environ["CHAT_ID"],
        body=request,
        files={
            "first": UploadSource.from_path(os.environ["PHOTO_1"]),
            "second": UploadSource.from_path(os.environ["PHOTO_2"]),
        },
        key=os.environ["REQUEST_KEY"],
    )
    for message in album.messages:
        print(message.message_id, message.media_group_id)
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
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let first = models::AlbumPhoto {
        caption: Some("<b>Trail day</b>".into()),
        caption_format: Some(models::AlbumPhotoCaptionFormat::Html),
        ..models::AlbumPhoto::new(FileInput::from_attachment("first")?)
    };
    let second = models::AlbumPhoto::new(FileInput::from_attachment("second")?);
    let request = models::SendAlbum::new(vec![
        models::AlbumItem::AlbumPhoto(Box::new(first)),
        models::AlbumItem::AlbumPhoto(Box::new(second)),
    ]);
    let files = vec![
        UploadPart::new("first", UploadSource::from_path(std::env::var("PHOTO_1")?)?)?,
        UploadPart::new("second", UploadSource::from_path(std::env::var("PHOTO_2")?)?)?,
    ];
    let album = bot
        .messages_send_album_multipart(&chat_id, &request, files, Some(&key))
        .await?;
    for message in &album.messages {
        println!("{} {:?}", message.message_id(), message.media_group_id());
    }
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The response contains `messages` in album order. Their IDs are consecutive and they share one `media_group_id`. Keep each `message_id`: you edit, delete, pin and reply to album items one message at a time.

## Combine media types <a href="#combine-media-types" id="combine-media-types"></a>

| Album | Items it can contain |
| --- | --- |
| Visual album | Any mix of `photo`, `video` and `live_photo` items. |
| Audio album | Only `audio` items. |
| Document album | Only `document` items. |

Send 2 to 10 items. A mix outside these families returns HTTP 400 `invalid_request`, and nothing is sent. Animations, voice messages, video notes and stickers cannot be album items; send them with `messages.send`.

## Item fields <a href="#item-fields" id="item-fields"></a>

Each item has a `type` and a `media` source. A `live_photo` item uses `media: {photo, video}`; every other item uses one `InputFile`.

| Field | Items | Rule |
| --- | --- | --- |
| `caption` | All | Up to 1,024 UTF-16 units after parsing. Each item has its own caption. |
| `caption_format` or `caption_entities` | All | Choose one. See [Formatting](../formatting.md#choose-the-formatting-fields). |
| `caption_above_media` | Photo, video, live photo | Shows the caption above the media. |
| `has_spoiler` | Photo, video, live photo | Hides the media behind a spoiler until the user taps it. |
| `thumbnail` | Video, audio, document | A new JPEG attachment. See [thumbnails](../files.md#media-types-and-sizes). |
| `cover` | Video | A photo shown before playback starts. |

The album request also accepts `reply_to`, `silent`, `protect_content`, `effect_id` and `topic_id`, with the same meaning as in [`messages.send`](../messages.md#request-fields). Albums do not accept `reply_markup`.

## Uploads and limits <a href="#uploads-and-limits" id="uploads-and-limits"></a>

| Limit | Value |
| --- | --- |
| Items per album | 2 to 10 |
| Binary parts per multipart album request | 30 |
| Complete HTTP body | 51 MiB (53,477,376 bytes) |
| Size and format of each file | Same as a single message. See [Files and media](../files.md#media-types-and-sizes). |

Each `attach` name must match one binary part. A thumbnail must be its own part; it cannot reuse a part that another source uses. If the files do not fit in one body, upload them first with `files.upload`, wait until they are ready, and send the album with their `file_id` values.

An album uses one send unit per item from your bot's global allowance and one send unit from the chat's allowance. See [Limits](../limits.md).

To retry an album request, send the same key, JSON payload and file bytes. Changing any of them under the same key returns HTTP 409 `idempotency_key_reused`.

## Work with album items <a href="#work-with-album-items" id="work-with-album-items"></a>

| Action | Behavior |
| --- | --- |
| Edit | Call `messages.edit` on one item. Replacement content must stay in the same family: a visual item stays a photo, video or live photo, an audio item stays audio, and a document stays a document. Otherwise the edit returns `album_type_mismatch`. See [Edit and delete](edit-and-delete.md). |
| Delete | Delete items one by one with `messages.delete`, or several at once with `messages.delete_batch`. |
| React | A reaction on any item applies to the first item that is still visible. See [Reactions and pins](reactions-and-pins.md). |
| Copy or forward | Copies and forwards keep the album grouping. See [Copy and forward](../copy-and-forward.md). |

## In groups and topics <a href="#in-groups-and-topics" id="in-groups-and-topics"></a>

Super Groups do not accept `messages.send_album`. Send group media one message at a time with `messages.send`. See [Groups](../groups.md).

Continue with the [message reference](../reference/messages.md#operation-messages-send-album) for the full request schema.
