# AlbumLivePhoto

The file source and caption settings for a live photo in an album.

Only the listed fields are allowed. Do not supply `caption_format` and `caption_entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [InputEntity](inputentity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `has_spoiler` | boolean | No | This is true when the media is hidden behind a spoiler. |
| `media` | object | Yes | This contains attached media or the named media sources for a rich message. Only the listed fields are allowed. |
| `media.photo` | [LiveInputFile](liveinputfile.md) | Yes | This contains the photo sizes or photo source for the object. |
| `media.video` | [LiveInputFile](liveinputfile.md) | Yes | This contains the video file or source for the content. |
| `type` | `"live_photo"` | Yes | The content type is live_photo. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
