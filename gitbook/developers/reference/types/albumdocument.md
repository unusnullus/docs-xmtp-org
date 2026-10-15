# AlbumDocument

The file source and caption settings for a document in an album.

Only the listed fields are allowed. Do not supply `caption_format` and `caption_entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_entities` | Array of [InputEntity](inputentity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `media` | [InputFile](inputfile.md) | Yes | This contains attached media or the named media sources for a rich message. |
| `thumbnail` | [InputThumbnail](inputthumbnail.md) | No | This contains the thumbnail file or source associated with the media. |
| `type` | `"document"` | Yes | The content type is document. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
