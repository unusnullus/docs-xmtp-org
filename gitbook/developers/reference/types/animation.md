# Animation

A received message containing an animation and its metadata.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `animation` | [MediaFile](mediafile.md) | Yes | This contains the animation file or source for the content. |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [Entity](entity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. The minimum is 1 item. |
| `has_spoiler` | boolean | No | This is true when the media is hidden behind a spoiler. |
| `type` | `"animation"` | Yes | The content type is animation. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
