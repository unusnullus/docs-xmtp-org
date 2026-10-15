---
description: "Upload, reuse and download media with the correct type and size limits."
---

# Files and media

You can upload a file once, wait for it to become ready and reuse its `file_id` in messages. You can also attach files directly to a message or ask ARX to fetch supported media from a public URL.

## Choose an input source <a href="#choose-an-input-source" id="choose-an-input-source"></a>

Each `InputFile` contains exactly one source:

| Source | Input | When to use it |
| --- | --- | --- |
| Existing file | `file_id` from an upload or a readable message. | Reuse a file that this bot can access, with the same media type. |
| New attachment | `{"attach":"picture"}` | Reference a named binary part in the same multipart request. |
| Public URL | `{"url":"https://example.com/image.jpg"}` | Fetch a supported type from a publicly reachable HTTP(S) source. |

Video notes, both parts of a live photo and chosen thumbnails reject URL inputs. A chosen thumbnail also rejects `file_id` reuse. Use a new attachment for it.

For a library upload, `files.upload` requires a binary part named `file` and a JSON part named `payload` with `Content-Type: application/json`. The payload requires `kind` and accepts an optional `file_name`. The upload kinds are `photo`, `video`, `animation`, `audio`, `voice`, `video_note`, `document` and `live_photo_video`. Thumbnails are attached to media rather than uploaded as separate library files.

For a multipart message, put the complete message body in the JSON `payload` part. Each `attach` value must match a supplied file-part name. The SDK's typed multipart methods build these parts for you. A message can contain up to 32 binary parts, and an album can contain up to 30. The whole HTTP body, including overhead, must fit within 51 MiB (53,477,376 bytes).

## Upload and send a document <a href="#upload-and-send-a-document" id="upload-and-send-a-document"></a>

The following example uploads a small text document, waits for processing and sends it to a chat that the user has already started. Set `ARX_BOT_TOKEN` and `CHAT_ID` in your environment. Use the chat ID from an incoming message update as `CHAT_ID`. Use `REQUEST_KEY` for the send and a separate `UPLOAD_REQUEST_KEY` for the upload. Choose new keys for a new upload or send, and keep them when retrying the same actions.

Install the [SDK for your language](sdks.md) before running the example.

{% tabs %}
{% tab title="TypeScript" %}

```typescript
import { Client, FileInput, UploadSource } from "@arx/nexo";

const bot = new Client({
  token: process.env.ARX_BOT_TOKEN!,
  baseUrl: "https://signal.arx.pro/bot-api/v1",
});
const uploaded = await bot.filesUpload(
  UploadSource.fromBytes(new TextEncoder().encode("Meeting notes.\n"), {
    filename: "notes.txt",
    contentType: "text/plain",
  }),
  {
    body: { kind: "document", file_name: "notes.txt" },
    key: process.env.UPLOAD_REQUEST_KEY!,
  },
);
const ready = await bot.waitFileReady(uploaded.file_id);
const message = await bot.messagesSend(process.env.CHAT_ID!, {
  body: {
    content: { type: "document", document: FileInput.fromId(ready.file_id) },
  },
  key: process.env.REQUEST_KEY!,
});
console.log(message.message_id);
```

{% endtab %}
{% tab title="Python" %}

```python
import os

from arx_nexo import Client, Config, FileInput, UploadSource, models

with Client(Config(base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    uploaded = bot.files_upload(
        UploadSource.from_bytes(
            b"Meeting notes.\n", filename="notes.txt", content_type="text/plain"
        ),
        body=models.UploadFile(kind="document", file_name="notes.txt"),
        key=os.environ["UPLOAD_REQUEST_KEY"],
    )
    ready = bot.wait_file_ready(uploaded.file_id)
    message = bot.messages_send(
        os.environ["CHAT_ID"],
        body=models.SendMessage(
            content=models.InputContent(
                root=models.InputDocument(
                    type="document", document=FileInput.from_id(ready.file_id)
                )
            )
        ),
        key=os.environ["REQUEST_KEY"],
    )
    print(message.message_id)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, FileInput, UploadSource, models, presence::Presence};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let bot = Client::new(Config::new(
        std::env::var("ARX_BOT_TOKEN")?,
        "https://signal.arx.pro/bot-api/v1",
        false,
    )?)?;
    let chat_id = std::env::var("CHAT_ID")?;
    let upload_key = std::env::var("UPLOAD_REQUEST_KEY")?;
    let send_key = std::env::var("REQUEST_KEY")?;
    let source = UploadSource::from_bytes(b"Meeting notes.\n".to_vec())?
        .with_filename("notes.txt")?
        .with_content_type("text/plain")?;
    let upload = models::UploadFile {
        kind: models::UploadFileKind::Document,
        file_name: Some("notes.txt".into()),
    };
    let uploaded = bot.files_upload(source, &upload, Some(&upload_key)).await?;
    let ready = bot.wait_file_ready(&uploaded.file_id, None).await?;
    let body = models::SendMessage {
        content: models::InputContent::InputDocument(Box::new(models::InputDocument {
            r#type: models::InputDocumentType::Document,
            document: FileInput::from_id(ready.file_id)?,
            caption: None,
            caption_entities: None,
            caption_format: None,
            disable_content_type_detection: None,
            thumbnail: None,
        })),
        effect_id: None,
        link_preview: Presence::Unset,
        protect_content: None,
        reply_markup: None,
        reply_to: None,
        silent: None,
    };
    let query = [];
    let message = bot
        .messages_send(&chat_id, &query, Some(&body), Some(&send_key))
        .await?;
    println!("{}", message.message_id());
    Ok(())
}
```

{% endtab %}
{% endtabs %}

An upload can return a `File` with `state: "processing"`. Replaying the upload returns its original receipt, which can still say `processing` after the file is ready. Use `files.get` with `metadata_only=true`, or the readiness helper shown above, to check the current state with backoff and a deadline. Sending an unready file returns HTTP 409 `file_not_ready` without publishing the message.

Your send returns the created message. You do not need to wait for a separate upload update. Receiving messages and other events follows the [update transport you selected](updates.md).

## Media types and sizes <a href="#media-types-and-sizes" id="media-types-and-sizes"></a>

ARX validates file contents and decoded media rather than trusting the filename or supplied MIME type. One MiB is 1,048,576 bytes.

| Kind | Accepted format and detected MIME | Upload maximum | URL maximum | Additional rules |
| --- | --- | --- | --- | --- |
| Photo | JPEG, PNG or WebP: `image/jpeg`, `image/png`, `image/webp`. | 10 MiB (10,485,760 bytes). | 5 MiB (5,242,880 bytes). | Width plus height at most 10,000 pixels, aspect ratio at most 20:1, and at most 40,000,000 pixels. |
| Video | MP4: `video/mp4`, with a supported video codec. | 50 MiB (52,428,800 bytes). | 20 MiB (20,971,520 bytes). | Supported codecs are H.264, HEVC, AV1 and MPEG-4. |
| Animation | GIF or H.264 MP4: `image/gif`, `video/mp4`. | 50 MiB. | 20 MiB. | The animation must have no audio stream. |
| Audio | MP3 or M4A: `audio/mpeg`, `audio/mp4`. | 50 MiB. | 20 MiB. | One MP3, AAC or Opus audio stream, with at most an embedded cover image. |
| Voice | OGG with Opus, MP3 or M4A: `audio/ogg`, `audio/mpeg`, `audio/mp4`. | 50 MiB. | 20 MiB. | The decoded audio codec must be Opus, MP3 or AAC. |
| Video note | Square MP4: `video/mp4`. | 50 MiB. | URLs are rejected. | At most 60 seconds, with no caption. |
| Document | Content-dependent, including `application/pdf`, `application/zip` and `application/octet-stream`. | 50 MiB. | 20 MiB. | Executables, scripts and active HTML, SVG or XML content are rejected. |
| Live-photo photo | JPEG, PNG or WebP. | 10 MiB. | URLs are rejected. | Send it together with the video component. |
| Live-photo video | MP4: `video/mp4`. | 10 MiB. | URLs are rejected. | At most ten seconds. |
| Chosen thumbnail | JPEG: `image/jpeg`. | 199,999 bytes. | URLs are rejected. | At most 320 pixels on either side, supplied as a new attachment. |

Decoded video and audio must have a positive duration of at most 86,400 seconds (24 hours), unless their type has a smaller bound above. Video dimensions must be positive and fit within 40,000,000 pixels. An animation's codec and lack of audio have additional validation, as shown in the table.

The 50 MiB ceiling does not apply to photos, live-photo videos or thumbnails. An oversized file returns HTTP 413 `file_too_large`, with the byte limit in `error.details.limit`. Reducing the body size alone will not fix an invalid media type. See the [file error catalog](errors.md#files-and-uploads).

Thumbnails are optional for types that support them. Omit the thumbnail to let ARX generate one. Refer to [Not available yet](not-available-yet.md) for unsupported media features.

## Captions and albums <a href="#captions-and-albums" id="captions-and-albums"></a>

Use the media's `caption` with either `caption_format` or `caption_entities`, following [Formatting](formatting.md). Captions allow up to 1,024 parsed UTF-16 units. `messages.edit` can change a caption or its position where supported, while retaining the original file. An empty caption clears its text and entities. Do not combine caption fields with `content` or `live_location` in the same edit.

`messages.send_album` accepts 2 to 10 items and creates them atomically with a shared `media_group_id`. Photos, videos and live photos can mix. Audio albums contain only audio, and document albums contain only documents. Albums have no keyboards. Ordinary items use `media: InputFile`; a live-photo item uses `media: {photo: InputFile, video: InputFile}`.

Upload files first and reuse their IDs if an album's binary parts would exceed the multipart body limit. Preserve the original idempotency key, JSON payload and bytes for retries. Changing any part with the same key returns `idempotency_key_reused`. The [API reference for files](reference/files.md) and [messages](reference/messages.md) has the complete typed request fields.

## Authenticated downloads <a href="#authenticated-downloads" id="authenticated-downloads"></a>

`files.download`, `GET /file_contents/{file_id}`, streams original bytes when you supply your bot token in the Authorization header. It takes no query parameters or JSON body. It returns bytes rather than a JSON success envelope or redirect.

Omit `Range` to retrieve the whole file. A single range can be bounded (`bytes=0-1023`), open-ended (`bytes=1024-`) or a suffix (`bytes=-1024`). Multiple ranges are rejected.

| HTTP result | Meaning |
| --- | --- |
| 200 | The response contains the complete original. |
| 206 | The response contains the requested bytes and a `Content-Range` header. |
| 416 | The range is unsupported or unsatisfiable. The body is empty and `Content-Range` is `bytes */size`. |
| 400 `download_too_large` | The complete original exceeds 20 MiB (20,971,520 bytes), even if you requested only one byte. |

Use `files.get` with `metadata_only=true` to inspect a larger file. Other failures before streaming use the JSON error envelope. Check the response length for truncation before using the file. You can retry or resume with a range while the file remains accessible.

Downloads return the validated MIME type, `Content-Length`, `Accept-Ranges: bytes` and `Cache-Control: private, no-store`. The default total deadline is 3,600 seconds, with a 30-second inactivity or backpressure timeout. Limit your own stream size and processing time as well.

A ready file can also include `download_url` and `download_url_expires_at`. Use that signed URL without an Authorization header and treat it as a credential. Its default lifetime is one hour, but revocation or loss of file access can invalidate it sooner. Call `files.get` to obtain a fresh URL when needed.

## Profile media <a href="#profile-media" id="profile-media"></a>

`profile.set_photo` requires a multipart JSON `payload` and one new attachment. It makes an independent copy. Use `type: "static"` with `photo: {attach: "photo"}`, or `type: "animated"` with `animation: {attach: "video"}`. File IDs and URLs are rejected for profile uploads.

| Profile media | Requirements |
| --- | --- |
| Static avatar | JPEG, PNG or WebP, up to 10 MiB. |
| Animated avatar | Square H.264 MP4, up to ten seconds and 10 MiB. |
| `main_frame_seconds` | Defaults to zero. It must be nonnegative, less than ten seconds and before the decoded video ends. This frame supplies the fallback JPEG. |
| Description media through NexoCreator | A photo or H.264 MP4, up to 10 MiB. The avatar's square and ten-second rules do not apply. Video uses frame zero. |

`profile.delete_photo` uses an empty JSON object to remove the avatar. Both profile operations return `true`. Use a new key for a new change and keep the key, body and file bytes for retries. See [the profile reference](reference/profile.md) for complete examples.

Replacing or removing profile media invalidates old profile references. For a test bot, only its owner and current testers can see its profile media. To manage these images in chat, follow [Profile media in NexoCreator](nexocreator.md#profile-media).

## User profile photos <a href="#user-profile-photos" id="user-profile-photos"></a>

`users.list_profile_photos` lets your bot request photos for a known user. It returns the current photo only, with zero or one item and `next_cursor: null`. It can include up to four sizes, smallest first, with file IDs scoped to your bot.

The `limit` range is 1 to 100. Omit `cursor` or pass an empty value. A known user without a photo or the required sharing consent produces an empty page. An unknown or deleted user returns HTTP 404 `user_not_found`.

For users known only through a user-selection message, access requires `share_photo: true` and a selection message that remains readable by your bot. See [Keyboards](keyboards.md) for the selection request and the [profile reference](reference/profile.md) for the operation.

## File identity and retention <a href="#file-identity-and-retention" id="file-identity-and-retention"></a>

| Identifier or source | Access and lifetime |
| --- | --- |
| `file_id` | An opaque credential for this bot's accessible file. It can download or resend the same media type. Ownership transfer preserves library IDs. |
| `file_unique_id` | A stable identity for comparing files across bots and time. It cannot download or resend a file. |
| Bot library | The default allowance is 5 GiB. Never-used uploads expire after 24 hours. Files may be deleted after a period without use. |
| User-shared file | Access depends on a readable message reference. Deleting the reference can make the file unavailable. |

Keep only the file data your service needs. Do not log file IDs or signed URLs, execute downloaded documents or assume a filename proves a file's type. Continue with [Security and privacy](security.md) for safe handling and [Errors](errors.md) for recovery decisions.
