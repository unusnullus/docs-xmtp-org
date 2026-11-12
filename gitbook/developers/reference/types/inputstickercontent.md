# InputStickerContent

Send a sticker through messages.send. Reuse an admitted sticker file_id, attach a new WEBP/TGS/WEBM file, or import a static WEBP URL. Stickers have no captions and cannot be used in albums or media edits.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `emoji` | string | No | Ignored for file_id and URL. For an original multipart upload only: NFC, exactly one emoji extended grapheme cluster, at most 64 UTF-8 bytes; invalid uploads fail with HTTP 400 before writes. |
| `sticker` | [InputFile](inputfile.md) | Yes | This contains the regular sticker file or source for the content. |
| `type` | `"sticker"` | Yes | The content type is sticker. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
