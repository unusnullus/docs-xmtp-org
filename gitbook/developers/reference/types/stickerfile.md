# StickerFile

Metadata for a sticker file, including its animation and video format indicators.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `emoji` | string | No | Optional association from original upload creation metadata, preserved on reuse. |
| `file_id` | string | Yes | Your bot can use this opaque identifier to send or retrieve the file. |
| `file_name` | string | No | This is the filename associated with the upload or returned file. |
| `file_size` | integer | No | This is the file size in bytes. The minimum is 1. |
| `file_unique_id` | string | Yes | This is the stable identifier for recognizing the same file. You cannot use it to download or send a file. |
| `height` | integer | Yes | This is the height of the media or layout, in pixels. The minimum is 1. The maximum is 512. |
| `is_animated` | boolean | Yes | Whether this sticker is animated. |
| `is_video` | boolean | Yes | Whether this sticker uses a video format. |
| `mime_type` | string | No | This is the MIME type of the file. |
| `type` | `"regular"` | Yes | The content type is regular. |
| `width` | integer | Yes | This is the width of the media or layout, in pixels. The minimum is 1. The maximum is 512. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
