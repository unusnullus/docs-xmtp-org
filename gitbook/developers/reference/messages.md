# Messages

Send, edit, copy, forward, delete, and pin messages in chats your bot can access.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`messages.send`](#operation-messages-send) | `POST /chats/{chat_id}/messages` |
| [`messages.send_album`](#operation-messages-send-album) | `POST /chats/{chat_id}/messages/album` |
| [`messages.copy`](#operation-messages-copy) | `POST /chats/{chat_id}/messages/copy` |
| [`messages.delete_batch`](#operation-messages-delete-batch) | `POST /chats/{chat_id}/messages/delete` |
| [`messages.forward`](#operation-messages-forward) | `POST /chats/{chat_id}/messages/forward` |
| [`messages.unpin`](#operation-messages-unpin) | `POST /chats/{chat_id}/messages/unpin` |
| [`messages.unpin_all`](#operation-messages-unpin-all) | `POST /chats/{chat_id}/messages/unpin_all` |
| [`messages.delete`](#operation-messages-delete) | `DELETE /chats/{chat_id}/messages/{message_id}` |
| [`messages.edit`](#operation-messages-edit) | `PATCH /chats/{chat_id}/messages/{message_id}` |
| [`messages.pin`](#operation-messages-pin) | `POST /chats/{chat_id}/messages/{message_id}/pin` |

## messages.send <a href="#operation-messages-send" id="operation-messages-send"></a>

`POST /bot-api/v1/chats/{chat_id}/messages`

Send a message to a private chat that the user has started. Use the chat ID from an update or a chat lookup.

Choose the content's form with `content.type`. Use JSON for text and existing library files or URL sources. To attach new media, send multipart with the JSON request in `payload` and binary parts matching the attachment names. Preserve the complete request and idempotency key when retrying a send.

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request" id="request"></a>

Content type: `application/json`. You must include the body.

[SendMessage](types/sendmessage.md)

The content and delivery settings for a new message. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `content` | [InputContent](types/inputcontent.md) | Yes | This contains the message content. The type field selects its variant. |
| `effect_id` | string | No | Use a message effect identifier returned by message_effects.list. The value must match `^[A-Za-z0-9_-]{1,64}$`. |
| `link_preview` | [InputLinkPreview](types/inputlinkpreview.md) or null | No | This contains link preview settings or the resulting preview metadata. |
| `protect_content` | boolean | No | Set this to true to protect the new message against copying and forwarding. |
| `reply_markup` | [ReplyMarkup](types/replymarkup.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `reply_to` | [InputReply](types/inputreply.md) | No | This identifies the reply target and optional quote for the message. |
| `silent` | boolean | No | Set this to true to send or pin the message without a notification sound. |

Content type: `multipart/form-data`. You must include the body.

Send the message body in the JSON `payload` part. Each `attach` value names a binary part in this request. You can attach up to 32 file parts. See [Files](../files.md) for media and thumbnail requirements. Let your HTTP library set the multipart boundary.

The request deadline is 3,600 seconds.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `payload` | [SendMessage](types/sendmessage.md) | Yes | Send the JSON operation body in the payload part of a multipart request. |

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Message](types/message.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

HTTP 403 `chat_not_started`, `bot_blocked_by_user`, or `user_deactivated` means you should stop attempting that send. HTTP 409 `file_not_ready` means you should wait for media processing. Use [Formatting](../formatting.md) for text rules, [Files](../files.md) for media, and [Receiving updates](../receiving-updates.md) for incoming messages.

### Example <a href="#example" id="example"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.sendText(chatId, "Hello", { key: requestKey });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.send_text(os.environ["CHAT_ID"], 'Hello', key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let result = bot.send_text(&chat_id, "Hello", Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"content": {"format": "plain", "text": "Hello", "type": "text"}}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "chat": {
      "id": "4398046511200",
      "kind": "private"
    },
    "content": {
      "entities": [],
      "text": "Hello",
      "type": "text"
    },
    "fallback_text": "Hello",
    "has_protected_content": false,
    "kind": "content",
    "message_id": "42",
    "revision": "1",
    "sender_user": {
      "id": "10000042",
      "is_bot": true,
      "name": "Acme Support",
      "username": "ACMESUPPORTBOT"
    },
    "sent_at": "2026-10-11T12:00:00Z",
    "silent": false,
    "state_version": "42"
  }
}
```

## messages.send_album <a href="#operation-messages-send-album" id="operation-messages-send-album"></a>

`POST /bot-api/v1/chats/{chat_id}/messages/album`

Send 2 to 10 media items as one album. The result contains one message for each accepted item.

You can mix photos, videos, and live photos. An audio album contains only audio, and a document album contains only documents. Use existing file IDs in JSON, or attach new files with multipart. Albums do not accept a reply keyboard or inline keyboard.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-1" id="request-1"></a>

Content type: `application/json`. You must include the body.

[SendAlbum](types/sendalbum.md)

The media items and delivery settings for a new album. Send 2 to 10 items together. You can mix photos, videos, and live photos. An audio or document album must contain only its own kind. The messages have consecutive IDs and share a media_group_id. The request uses one global send unit per item and one chat send unit per album. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `effect_id` | string | No | Use a message effect identifier returned by message_effects.list. |
| `items` | Array of [AlbumItem](types/albumitem.md) | Yes | These are the items in their returned or requested order. The minimum is 2 items. The maximum is 10 items. |
| `protect_content` | boolean | No | Set this to true to protect the new message against copying and forwarding. |
| `reply_to` | [InputReply](types/inputreply.md) | No | This identifies the reply target and optional quote for the message. |
| `silent` | boolean | No | Set this to true to send or pin the message without a notification sound. |

Content type: `multipart/form-data`. You must include the body.

Send the album body in the JSON `payload` part, with binary parts for attached media. Each attachment name must match its part. You can attach up to 30 file parts. See [Files](../files.md) for album uploads. Let your HTTP library set the multipart boundary.

The request deadline is 3,600 seconds.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `payload` | [SendAlbum](types/sendalbum.md) | Yes | Send the JSON operation body in the payload part of a multipart request. |

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Album](types/album.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 400 `album_type_mismatch` means you must use a supported combination of item types. HTTP 409 `file_not_ready` means an item still needs processing. See [Files](../files.md) for media limits and album uploads.

### Example <a href="#example-1" id="example-1"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `PHOTO_FILE_ID`, `REQUEST_KEY`, `SECOND_PHOTO_FILE_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models, FileInput } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const photoFileId = process.env.PHOTO_FILE_ID;
const requestKey = process.env.REQUEST_KEY;
const secondPhotoFileId = process.env.SECOND_PHOTO_FILE_ID;
if (!arxBotToken || !chatId || !photoFileId || !requestKey || !secondPhotoFileId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, PHOTO_FILE_ID, REQUEST_KEY, SECOND_PHOTO_FILE_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.SendAlbum = {
    "items": [{
      "type": "photo",
      "media": FileInput.fromId(photoFileId)
    }, {
      "type": "photo",
      "media": FileInput.fromId(secondPhotoFileId)
    }]
  };
  const result = await bot.messagesSendAlbum(chatId, { body, key: requestKey });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models, FileInput

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.SendAlbum(
        items=[models.AlbumItem(
            root=models.AlbumPhoto(type='photo', media=FileInput.from_id(os.environ["PHOTO_FILE_ID"])),
        ), models.AlbumItem(
            root=models.AlbumPhoto(
                type='photo',
                media=FileInput.from_id(os.environ["SECOND_PHOTO_FILE_ID"]),
            ),
        )],
    )
    result = bot.messages_send_album(os.environ["CHAT_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models, FileInput };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let photo_file_id = std::env::var("PHOTO_FILE_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let second_photo_file_id = std::env::var("SECOND_PHOTO_FILE_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::SendAlbum {
        effect_id: None,
        items: vec![models::AlbumItem::AlbumPhoto(Box::new(models::AlbumPhoto {
            caption: None,
            caption_above_media: None,
            caption_entities: None,
            caption_format: None,
            has_spoiler: None,
            media: FileInput::from_id(photo_file_id)?,
            r#type: models::AlbumPhotoType::Photo,
        })), models::AlbumItem::AlbumPhoto(Box::new(models::AlbumPhoto {
            caption: None,
            caption_above_media: None,
            caption_entities: None,
            caption_format: None,
            has_spoiler: None,
            media: FileInput::from_id(second_photo_file_id)?,
            r#type: models::AlbumPhotoType::Photo,
        }))],
        protect_content: None,
        reply_to: None,
        silent: None,
    };
    let query = [];
    let result = bot.messages_send_album(&chat_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
payload=$(python3 - <<'PY'
import json
import os
print(json.dumps({'items': [{'type': 'photo', 'media': {'file_id': os.environ['PHOTO_FILE_ID']}}, {'type': 'photo', 'media': {'file_id': os.environ['SECOND_PHOTO_FILE_ID']}}]}))
PY
)

curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/album" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data "${payload}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "messages": [
      {
        "chat": {
          "id": "4398046511200",
          "kind": "private"
        },
        "content": {
          "photo": [
            {
              "file_id": "rt1.c2FtcGxlLXBob3RvLTE.QMCkB_dy7FgONBWySzPfghEC-zlXOqY6sGXdKhXzr5k",
              "file_size": 1024,
              "file_unique_id": "FdwQp33nil58WVXrOLvLDYRDqfQQ-McJommfhZbxoZQ",
              "height": 480,
              "mime": "image/jpeg",
              "width": 640
            }
          ],
          "type": "photo"
        },
        "fallback_text": "Example",
        "has_protected_content": false,
        "kind": "content",
        "media_group_id": "example-album-1",
        "message_id": "42",
        "revision": "1",
        "sender_user": {
          "id": "10000042",
          "is_bot": true,
          "name": "Acme Support",
          "username": "ACMESUPPORTBOT"
        },
        "sent_at": "2026-10-11T12:00:00Z",
        "silent": false,
        "state_version": "42"
      },
      {
        "chat": {
          "id": "4398046511200",
          "kind": "private"
        },
        "content": {
          "photo": [
            {
              "file_id": "rt1.c2FtcGxlLXBob3RvLTI.NPSV1aJC5gQA1kySsvwknIqBlvaDMszdv5WEy00RXRE",
              "file_size": 1024,
              "file_unique_id": "PgFBmbHreTP5gfMJLkST7yVTqfezFcscJC4xkWMgGy4",
              "height": 480,
              "mime": "image/jpeg",
              "width": 640
            }
          ],
          "type": "photo"
        },
        "fallback_text": "Example",
        "has_protected_content": false,
        "kind": "content",
        "media_group_id": "example-album-1",
        "message_id": "43",
        "revision": "1",
        "sender_user": {
          "id": "10000042",
          "is_bot": true,
          "name": "Acme Support",
          "username": "ACMESUPPORTBOT"
        },
        "sent_at": "2026-10-11T12:00:00Z",
        "silent": false,
        "state_version": "42"
      }
    ]
  }
}
```

## messages.copy <a href="#operation-messages-copy" id="operation-messages-copy"></a>

`POST /bot-api/v1/chats/{chat_id}/messages/copy`

Copy messages into a chat without linking them to their origin. Supply 1 to 100 strictly increasing source message IDs.

Albums remain albums. To replace a caption or inline keyboard, copy a single message. A replacement caption uses either `caption_format` or `caption_entities`. An empty caption removes its text; `remove_caption: true` removes the original caption and cannot accompany a replacement caption. Invalid options reject the request before any messages are copied.

Missing, service, protected, paid media, invoice, and giveaway messages are skipped. Check the result's `skipped` list even when the request succeeds.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-2" id="request-2"></a>

Content type: `application/json`. You must include the body.

[CopyMessages](types/copymessages.md)

The source message IDs and optional replacements for a copy request. Only the listed fields are allowed. If `caption_entities` is present, also include `caption`. If `caption_format` is present, also include `caption`. If any of `reply_markup` or `caption` is present, `message_ids` must contain at most 1 item. Do not supply `caption_format` and `caption_entities` together. If `remove_caption` is `true`, omit `caption`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [InputEntity](types/inputentity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `effect_id` | string | No | Use a message effect identifier returned by message_effects.list. |
| `from_chat_id` | string | Yes | This is the ID of the chat containing the messages that you want to copy or forward. The value must match `^[1-9][0-9]*$`. |
| `message_ids` | Array of string | Yes | Supply source message IDs as decimal strings in strictly increasing order. The minimum is 1 item. The maximum is 100 items. Each item must be unique. |
| `message_ids[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |
| `protect_content` | boolean | No | Set this to true to protect the new message against copying and forwarding. |
| `remove_caption` | boolean | No | Set this to true to remove the source caption from the copied message. |
| `reply_markup` | [ReplyMarkup](types/replymarkup.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `reply_to` | [InputReply](types/inputreply.md) | No | This identifies the reply target and optional quote for the message. |
| `silent` | boolean | No | Set this to true to send or pin the message without a notification sound. |
| `video_start_seconds` | number | No | This is the initial video playback position for the copied or forwarded message, in seconds. Set a new video playback start before the end of the video. Other message types are unchanged. The minimum is 0. |

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [MessageBatch](types/messagebatch.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

If no eligible messages remain, HTTP 403 `protected_content` means at least one source was protected; otherwise you receive HTTP 404 `message_not_found`. HTTP 400 `invalid_parameter` identifies an invalid copy option. See [Copy and forward](../copy-and-forward.md) for video offsets, caption position, and reply options.

### Example <a href="#example-2" id="example-2"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `MESSAGE_IDS`, `REQUEST_KEY`, `SOURCE_CHAT_ID` before running this example. Use a comma-separated, strictly increasing list for `MESSAGE_IDS`.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const messageIdsInput = process.env.MESSAGE_IDS;
const requestKey = process.env.REQUEST_KEY;
const sourceChatId = process.env.SOURCE_CHAT_ID;
if (!arxBotToken || !chatId || !messageIdsInput || !requestKey || !sourceChatId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, MESSAGE_IDS, REQUEST_KEY, SOURCE_CHAT_ID");
}
const messageIds = messageIdsInput.split(",");

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.CopyMessages = {
    "from_chat_id": sourceChatId,
    "message_ids": messageIds
  };
  const result = await bot.messagesCopy(chatId, { body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.CopyMessages(
        from_chat_id=os.environ["SOURCE_CHAT_ID"],
        message_ids=os.environ["MESSAGE_IDS"].split(","),
    )
    result = bot.messages_copy(os.environ["CHAT_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let message_ids : Vec<String> = std::env::var("MESSAGE_IDS")?.split(',').map(str::to_owned).collect();
    let request_key = std::env::var("REQUEST_KEY")?;
    let source_chat_id = std::env::var("SOURCE_CHAT_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::CopyMessages {
        caption: None,
        caption_above_media: None,
        caption_entities: None,
        caption_format: None,
        effect_id: None,
        from_chat_id: source_chat_id,
        message_ids: message_ids,
        protect_content: None,
        remove_caption: None,
        reply_markup: None,
        reply_to: None,
        silent: None,
        video_start_seconds: None,
    };
    let query = [];
    let result = bot.messages_copy(&chat_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
payload=$(python3 - <<'PY'
import json
import os
print(json.dumps({'from_chat_id': os.environ['SOURCE_CHAT_ID'], 'message_ids': os.environ['MESSAGE_IDS'].split(",")}))
PY
)

curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/copy" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data "${payload}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "messages": [
      {
        "chat": {
          "id": "4398046511201",
          "kind": "private"
        },
        "content": {
          "entities": [],
          "text": "Hello",
          "type": "text"
        },
        "fallback_text": "Hello",
        "has_protected_content": false,
        "kind": "content",
        "message_id": "100",
        "revision": "1",
        "sender_user": {
          "id": "10000042",
          "is_bot": true,
          "name": "Acme Support",
          "username": "ACMESUPPORTBOT"
        },
        "sent_at": "2026-10-11T12:00:00Z",
        "silent": false,
        "state_version": "42"
      }
    ],
    "skipped": []
  }
}
```

## messages.delete_batch <a href="#operation-messages-delete-batch" id="operation-messages-delete-batch"></a>

`POST /bot-api/v1/chats/{chat_id}/messages/delete`

Attempt to delete up to 100 messages in one chat. The response separates deleted IDs from skipped IDs.

The same age and access rules as `messages.delete` apply to each item. Missing or undeletable messages appear in `skipped`, so you should inspect both result lists after a successful request. See [the single-message operation](messages.md#operation-messages-delete) for deletion rules.

### Parameters <a href="#parameters-3" id="parameters-3"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-3" id="request-3"></a>

Content type: `application/json`. You must include the body.

[DeleteMessages](types/deletemessages.md)

The message IDs to remove from one chat. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `message_ids` | Array of string | Yes | These are message IDs within the specified chat, encoded as decimal strings. The minimum is 1 item. The maximum is 100 items. Each item must be unique. |
| `message_ids[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |

### Response <a href="#response-3" id="response-3"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [DeleteBatch](types/deletebatch.md) in `result` |

### Errors <a href="#errors-3" id="errors-3"></a>

HTTP 400 `invalid_request` means the message list is invalid. On success, inspect `skipped` for individual messages that could not be deleted.

### Example <a href="#example-3" id="example-3"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `MESSAGE_IDS`, `REQUEST_KEY` before running this example. Use a comma-separated, strictly increasing list for `MESSAGE_IDS`.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const messageIdsInput = process.env.MESSAGE_IDS;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !messageIdsInput || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, MESSAGE_IDS, REQUEST_KEY");
}
const messageIds = messageIdsInput.split(",");

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.DeleteMessages = {
    "message_ids": messageIds
  };
  const result = await bot.messagesDeleteBatch(chatId, { body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.DeleteMessages(message_ids=os.environ["MESSAGE_IDS"].split(","))
    result = bot.messages_delete_batch(os.environ["CHAT_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let message_ids : Vec<String> = std::env::var("MESSAGE_IDS")?.split(',').map(str::to_owned).collect();
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::DeleteMessages {
        message_ids: message_ids,
    };
    let query = [];
    let result = bot.messages_delete_batch(&chat_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
payload=$(python3 - <<'PY'
import json
import os
print(json.dumps({'message_ids': os.environ['MESSAGE_IDS'].split(",")}))
PY
)

curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/delete" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data "${payload}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "deleted": [
      "42"
    ],
    "skipped": [
      "43"
    ]
  }
}
```

## messages.forward <a href="#operation-messages-forward" id="operation-messages-forward"></a>

`POST /bot-api/v1/chats/{chat_id}/messages/forward`

Forward messages with their source attribution. Supply 1 to 100 strictly increasing source message IDs.

The API skips missing, service, protected, paid media, invoice, and giveaway messages and reports skipped IDs. Private origins can hide the original user's identity. A forward keeps the source caption. Use `messages.copy` to replace a caption without attribution. Invalid video offsets reject the request before forwarding any messages.

### Parameters <a href="#parameters-4" id="parameters-4"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-4" id="request-4"></a>

Content type: `application/json`. You must include the body.

[ForwardMessages](types/forwardmessages.md)

The source message IDs and delivery options for a forward request. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `effect_id` | string | No | Use a message effect identifier returned by message_effects.list. |
| `from_chat_id` | string | Yes | This is the ID of the chat containing the messages that you want to copy or forward. The value must match `^[1-9][0-9]*$`. |
| `message_ids` | Array of string | Yes | Supply source message IDs as decimal strings in strictly increasing order. The minimum is 1 item. The maximum is 100 items. Each item must be unique. |
| `message_ids[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |
| `protect_content` | boolean | No | Set this to true to protect the new message against copying and forwarding. |
| `silent` | boolean | No | Set this to true to send or pin the message without a notification sound. |
| `video_start_seconds` | number | No | This is the initial video playback position for the copied or forwarded message, in seconds. Set a new video playback start before the end of the video. Other message types are unchanged. The minimum is 0. |

### Response <a href="#response-4" id="response-4"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [MessageBatch](types/messagebatch.md) in `result` |

### Errors <a href="#errors-4" id="errors-4"></a>

If no eligible messages remain, HTTP 403 `protected_content` means at least one source was protected; otherwise you receive HTTP 404 `message_not_found`. Check the response's `skipped` list even when the request succeeds. See [Copy and forward](../copy-and-forward.md) for batch behavior and reply options.

### Example <a href="#example-4" id="example-4"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `MESSAGE_IDS`, `REQUEST_KEY`, `SOURCE_CHAT_ID` before running this example. Use a comma-separated, strictly increasing list for `MESSAGE_IDS`.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const messageIdsInput = process.env.MESSAGE_IDS;
const requestKey = process.env.REQUEST_KEY;
const sourceChatId = process.env.SOURCE_CHAT_ID;
if (!arxBotToken || !chatId || !messageIdsInput || !requestKey || !sourceChatId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, MESSAGE_IDS, REQUEST_KEY, SOURCE_CHAT_ID");
}
const messageIds = messageIdsInput.split(",");

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.ForwardMessages = {
    "from_chat_id": sourceChatId,
    "message_ids": messageIds
  };
  const result = await bot.messagesForward(chatId, { body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.ForwardMessages(
        from_chat_id=os.environ["SOURCE_CHAT_ID"],
        message_ids=os.environ["MESSAGE_IDS"].split(","),
    )
    result = bot.messages_forward(os.environ["CHAT_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let message_ids : Vec<String> = std::env::var("MESSAGE_IDS")?.split(',').map(str::to_owned).collect();
    let request_key = std::env::var("REQUEST_KEY")?;
    let source_chat_id = std::env::var("SOURCE_CHAT_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::ForwardMessages {
        effect_id: None,
        from_chat_id: source_chat_id,
        message_ids: message_ids,
        protect_content: None,
        silent: None,
        video_start_seconds: None,
    };
    let query = [];
    let result = bot.messages_forward(&chat_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
payload=$(python3 - <<'PY'
import json
import os
print(json.dumps({'from_chat_id': os.environ['SOURCE_CHAT_ID'], 'message_ids': os.environ['MESSAGE_IDS'].split(",")}))
PY
)

curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/forward" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data "${payload}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "messages": [
      {
        "chat": {
          "id": "4398046511201",
          "kind": "private"
        },
        "content": {
          "entities": [],
          "text": "Hello",
          "type": "text"
        },
        "fallback_text": "Hello",
        "has_protected_content": false,
        "kind": "content",
        "message_id": "100",
        "revision": "1",
        "sender_user": {
          "id": "10000042",
          "is_bot": true,
          "name": "Acme Support",
          "username": "ACMESUPPORTBOT"
        },
        "sent_at": "2026-10-11T12:00:00Z",
        "silent": false,
        "state_version": "42",
        "forward_origin": {
          "type": "hidden_user",
          "sender_name": "Example",
          "sent_at": "2026-10-11T12:00:00Z"
        }
      }
    ],
    "skipped": []
  }
}
```

## messages.unpin <a href="#operation-messages-unpin" id="operation-messages-unpin"></a>

`POST /bot-api/v1/chats/{chat_id}/messages/unpin`

Remove a message from the chat's pinned list. Supply its message ID, or omit the ID to remove the most recent pin.

This request does not delete the message. Use `messages.unpin_all` when you want to clear the complete pinned list.

### Parameters <a href="#parameters-5" id="parameters-5"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-5" id="request-5"></a>

Content type: `application/json`. You may omit the body.

[UnpinMessage](types/unpinmessage.md)

The optional message ID selecting which pinned message to remove. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `message_id` | string | No | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Response <a href="#response-5" id="response-5"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-5" id="errors-5"></a>

HTTP 400 `invalid_request` means the optional message ID or body is malformed. Use a decimal message ID. If the ID is not pinned, the operation leaves the pinned list unchanged.

### Example <a href="#example-5" id="example-5"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `MESSAGE_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const messageId = process.env.MESSAGE_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !messageId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, MESSAGE_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.UnpinMessage = {
    "message_id": messageId
  };
  const result = await bot.messagesUnpin(chatId, { body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.UnpinMessage(message_id=os.environ["MESSAGE_ID"])
    result = bot.messages_unpin(os.environ["CHAT_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let message_id = std::env::var("MESSAGE_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::UnpinMessage {
        message_id: Some(message_id),
    };
    let query = [];
    let result = bot.messages_unpin(&chat_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
payload=$(python3 - <<'PY'
import json
import os
print(json.dumps({'message_id': os.environ['MESSAGE_ID']}))
PY
)

curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/unpin" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data "${payload}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": true
}
```

## messages.unpin_all <a href="#operation-messages-unpin-all" id="operation-messages-unpin-all"></a>

`POST /bot-api/v1/chats/{chat_id}/messages/unpin_all`

Remove all pins from the selected chat. Omit the body or send `{}`.

The messages themselves remain in the conversation. Use `messages.unpin` when you only want to remove one pin.

### Parameters <a href="#parameters-6" id="parameters-6"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-6" id="request-6"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-6" id="response-6"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-6" id="errors-6"></a>

HTTP 404 `chat_not_found` means your bot cannot access the selected chat. HTTP 403 `chat_not_started` means the user must start the chat first.

### Example <a href="#example-6" id="example-6"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.messagesUnpinAll(chatId, { key: requestKey });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.messages_unpin_all(os.environ["CHAT_ID"], key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.messages_unpin_all(&chat_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/unpin_all" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": true
}
```

## messages.delete <a href="#operation-messages-delete" id="operation-messages-delete"></a>

`DELETE /bot-api/v1/chats/{chat_id}/messages/{message_id}`

Delete one message your bot can access.

Your bot can delete its own private-chat messages without an age limit. Incoming private-chat messages must be less than 48 hours old. A private dice message must be more than 24 hours old.

### Parameters <a href="#parameters-7" id="parameters-7"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `message_id` | path | string | Yes | Use the ID of the message within this chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-7" id="request-7"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-7" id="response-7"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-7" id="errors-7"></a>

HTTP 403 `message_too_old` or `message_cant_be_deleted` means you cannot delete the target under these rules. HTTP 404 `message_not_found` means the message is missing or inaccessible. Use `messages.delete_batch` when you want to attempt several IDs and inspect skipped items.

### Example <a href="#example-7" id="example-7"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `MESSAGE_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const messageId = process.env.MESSAGE_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !messageId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, MESSAGE_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.messagesDelete(chatId, messageId, { key: requestKey });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.messages_delete(os.environ["CHAT_ID"], os.environ["MESSAGE_ID"], key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let message_id = std::env::var("MESSAGE_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.messages_delete(&chat_id, &message_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X DELETE \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/${MESSAGE_ID}" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": true
}
```

## messages.edit <a href="#operation-messages-edit" id="operation-messages-edit"></a>

`PATCH /bot-api/v1/chats/{chat_id}/messages/{message_id}`

Edit a message sent by your bot. You can replace its content, update its inline keyboard, or edit the supported caption or live-location fields.

Omitted fields preserve their values. Inline `rows: []` removes buttons. A replacement keyboard preserves `open_reply` when you omit it; you cannot change its accepted value. Use `expected_revision` to avoid overwriting another edit. An identical edit returns the current message without advancing its revision.

### Parameters <a href="#parameters-8" id="parameters-8"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `message_id` | path | string | Yes | Use the ID of the message within this chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-8" id="request-8"></a>

Content type: `application/json`. You must include the body.

[EditMessage](types/editmessage.md)

The content, caption, live location, or inline keyboard changes to apply to a message. Choose one content change: replace content, edit caption fields, or update live_location. You can also change the inline keyboard. Caption edits preserve the media. Omit caption to keep its text, and change only caption_above_media to preserve its entities. You can place captions above photos, videos, and animations. Omit reply_markup to keep the keyboard, or set its rows to [] to remove buttons. You cannot change open_reply. An edit that leaves the message unchanged keeps its revision. A change increments the revision once. Only the listed fields are allowed. Do not supply `content` and `live_location` together. If `link_preview` is present, also include `content`. Do not supply `caption_format` and `caption_entities` together. If any of `caption`, `caption_format`, `caption_entities`, or `caption_above_media` is present, omit `content` and `live_location`. Include at least one of `content`, `reply_markup`, `live_location`, `caption`, `caption_format`, `caption_entities`, or `caption_above_media`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [InputEntity](types/inputentity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `content` | [InputEditContent](types/inputeditcontent.md) | No | This contains the message content. The type field selects its variant. |
| `expected_revision` | string | No | Set the message revision that you expect to edit. A different current revision returns revision_conflict. The value must match `^[1-9][0-9]*$`. |
| `link_preview` | [InputLinkPreview](types/inputlinkpreview.md) or null | No | This contains link preview settings or the resulting preview metadata. |
| `live_location` | [EditLiveLocation](types/editlivelocation.md) | No | Supply new coordinates and optional settings for an active live location. |
| `reply_markup` | [InputInlineKeyboard](types/inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |

Content type: `multipart/form-data`. You must include the body.

Send the edit body in the JSON `payload` part. Attach replacement media in binary parts named by their `attach` values. You can attach up to 32 file parts. See [Files](../files.md) for media and caption edits. Let your HTTP library set the multipart boundary.

The request deadline is 3,600 seconds.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `payload` | [EditMessage](types/editmessage.md) | Yes | Send the JSON operation body in the payload part of a multipart request. |

### Response <a href="#response-8" id="response-8"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Message](types/message.md) in `result` |

### Errors <a href="#errors-8" id="errors-8"></a>

HTTP 400 `message_not_editable` means the message cannot accept this edit. HTTP 409 `revision_conflict` means you should use the current message state before editing again. HTTP 409 `file_not_ready` means you must wait for media processing. See [Files](../files.md) for caption edits and [Locations and contacts](../locations-and-contacts.md) for live-location edits.

### Example <a href="#example-8" id="example-8"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `MESSAGE_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const messageId = process.env.MESSAGE_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !messageId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, MESSAGE_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.EditMessage = {
    "content": {
      "type": "text",
      "text": "Hello again",
      "format": "plain"
    }
  };
  const result = await bot.messagesEdit(chatId, messageId, { body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.EditMessage(
        content=models.InputEditContent(
            root=models.InputText(type='text', text='Hello again', format='plain'),
        ),
    )
    result = bot.messages_edit(os.environ["CHAT_ID"], os.environ["MESSAGE_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let message_id = std::env::var("MESSAGE_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::EditMessage {
        caption: None,
        caption_above_media: None,
        caption_entities: None,
        caption_format: None,
        content: Some(models::InputEditContent::InputText(Box::new(models::InputText {
            entities: None,
            format: Some(models::InputTextFormat::Plain),
            text: "Hello again".to_owned(),
            r#type: models::InputTextType::Text,
        }))),
        expected_revision: None,
        link_preview: arx_nexo::presence::Presence::Unset,
        live_location: None,
        reply_markup: None,
    };
    let query = [];
    let result = bot.messages_edit(&chat_id, &message_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PATCH \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/${MESSAGE_ID}" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"content": {"type": "text", "text": "Hello again", "format": "plain"}}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "chat": {
      "id": "4398046511200",
      "kind": "private"
    },
    "content": {
      "entities": [],
      "text": "Hello again",
      "type": "text"
    },
    "fallback_text": "Hello again",
    "has_protected_content": false,
    "kind": "content",
    "message_id": "42",
    "revision": "2",
    "sender_user": {
      "id": "10000042",
      "is_bot": true,
      "name": "Acme Support",
      "username": "ACMESUPPORTBOT"
    },
    "sent_at": "2026-10-11T12:00:00Z",
    "silent": false,
    "state_version": "43"
  }
}
```

## messages.pin <a href="#operation-messages-pin" id="operation-messages-pin"></a>

`POST /bot-api/v1/chats/{chat_id}/messages/{message_id}/pin`

Pin an accessible message in a private chat. Use `silent: true` to suppress the notification sound.

### Parameters <a href="#parameters-9" id="parameters-9"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `message_id` | path | string | Yes | Use the ID of the message within this chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-9" id="request-9"></a>

Content type: `application/json`. You may omit the body.

[PinMessage](types/pinmessage.md)

The notification settings for pinning a message. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `silent` | boolean | No | Set this to true to send or pin the message without a notification sound. |

### Response <a href="#response-9" id="response-9"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-9" id="errors-9"></a>

The chat metadata includes its pinned message IDs. HTTP 409 `pin_limit_reached` means you should unpin an existing message before trying again. HTTP 404 `message_not_found` means the target is missing or inaccessible. Use `messages.unpin` to remove one pin.

### Example <a href="#example-9" id="example-9"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `MESSAGE_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const messageId = process.env.MESSAGE_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !messageId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, MESSAGE_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.PinMessage = {
    "silent": true
  };
  const result = await bot.messagesPin(chatId, messageId, { body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.PinMessage(silent=True)
    result = bot.messages_pin(os.environ["CHAT_ID"], os.environ["MESSAGE_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let message_id = std::env::var("MESSAGE_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::PinMessage {
        silent: Some(true),
    };
    let query = [];
    let result = bot.messages_pin(&chat_id, &message_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/${MESSAGE_ID}/pin" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"silent": true}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": true
}
```

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
