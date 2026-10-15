# LivePhoto

A photo and its associated short video returned as one live photo message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [Entity](entity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. The minimum is 1 item. |
| `has_spoiler` | boolean | No | This is true when the media is hidden behind a spoiler. |
| `live_photo` | object | Yes | This contains the photo and video files that together form a live photo. |
| `live_photo.photo` | [Photo](photo.md) | Yes | This contains the photo sizes or photo source for the object. |
| `live_photo.video` | [MediaFile](mediafile.md) | Yes | This contains the video file or source for the content. |
| `type` | `"live_photo"` | Yes | The content type is live_photo. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
