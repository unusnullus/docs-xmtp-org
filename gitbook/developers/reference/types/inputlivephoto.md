# InputLivePhoto

The photo and short video sources to send together as one live photo. Provide a photo and an MP4 video uploaded with kind live_photo_video. The video must be at most 10 MiB and 10 seconds long. Neither source accepts a URL.

Only the listed fields are allowed. Do not supply `caption_format` and `caption_entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [InputEntity](inputentity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `has_spoiler` | boolean | No | This is true when the media is hidden behind a spoiler. |
| `photo` | [LiveInputFile](liveinputfile.md) | Yes | This contains the photo sizes or photo source for the object. |
| `type` | `"live_photo"` | Yes | The content type is live_photo. |
| `video` | [LiveInputFile](liveinputfile.md) | Yes | This contains the video file or source for the content. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
