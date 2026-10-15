# InputRichAnimation

An animation block with optional caption text. Use this node when sending rich content.

Only the listed fields are allowed. Include exactly one of `file` or `media_id`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | [InputRichCaption](inputrichcaption.md) | No | This is the caption displayed with the media. |
| `file` | [InputFile](inputfile.md) | No | The source of this rich media item. Supply an existing file_id, the attach name of a binary part in this request, or a public URL where URL imports are supported. |
| `has_spoiler` | boolean | No | This is true when the media is hidden behind a spoiler. |
| `media_id` | string | No | Use the ID of a source in this rich message's media dictionary instead of file. |
| `type` | `"animation"` | Yes | The rich content node type is animation. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
