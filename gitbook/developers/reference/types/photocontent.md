# PhotoContent

The photo content returned within a message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [Entity](entity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. The minimum is 1 item. |
| `has_spoiler` | boolean | No | This is true when the media is hidden behind a spoiler. |
| `photo` | [Photo](photo.md) | Yes | This contains the photo sizes or photo source for the object. |
| `type` | `"photo"` | Yes | The content type is photo. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
