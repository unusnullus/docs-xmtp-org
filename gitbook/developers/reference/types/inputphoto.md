# InputPhoto

The file source and message settings for sending a photo.

Only the listed fields are allowed. Do not supply `caption_format` and `caption_entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [InputEntity](inputentity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `has_spoiler` | boolean | No | This is true when the media is hidden behind a spoiler. |
| `photo` | [InputFile](inputfile.md) | Yes | This contains the photo sizes or photo source for the object. |
| `type` | `"photo"` | Yes | The content type is photo. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
