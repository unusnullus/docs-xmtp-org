# InputAnimation

The file source and message settings for sending an animation.

Only the listed fields are allowed. Do not supply `caption_format` and `caption_entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `animation` | [InputFile](inputfile.md) | Yes | This contains the animation file or source for the content. |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [InputEntity](inputentity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `duration_seconds` | number | No | You can supply a duration hint in seconds. The response uses the duration measured from the file. The minimum is 0. The maximum is 86,400. |
| `has_spoiler` | boolean | No | This is true when the media is hidden behind a spoiler. |
| `height` | integer | No | You can supply a dimension hint in pixels. The response uses the dimensions measured from the file. The minimum is 0. The maximum is 10,000. |
| `thumbnail` | [InputThumbnail](inputthumbnail.md) | No | This contains the thumbnail file or source associated with the media. |
| `type` | `"animation"` | Yes | The content type is animation. |
| `width` | integer | No | You can supply a dimension hint in pixels. The response uses the dimensions measured from the file. The minimum is 0. The maximum is 10,000. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
