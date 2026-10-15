# RichVideo

A video block with optional caption text.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | [RichCaption](richcaption.md) | No | This is the caption displayed with the media. |
| `file` | [MediaFile](mediafile.md) | Yes | The file metadata for this rich media item, including the file_id you can reuse. |
| `has_spoiler` | boolean | No | This is true when the media is hidden behind a spoiler. |
| `type` | `"video"` | Yes | The rich content node type is video. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
