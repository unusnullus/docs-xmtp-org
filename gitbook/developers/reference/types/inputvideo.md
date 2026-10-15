# InputVideo

The file source and message settings for sending a video.

Only the listed fields are allowed. Do not supply `caption_format` and `caption_entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [InputEntity](inputentity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `cover` | [InputFile](inputfile.md) | No | This is the cover image for the video. |
| `duration_seconds` | number | No | You can supply a duration hint in seconds. The response uses the duration measured from the file. The minimum is 0. The maximum is 86,400. |
| `has_spoiler` | boolean | No | This is true when the media is hidden behind a spoiler. |
| `height` | integer | No | You can supply a dimension hint in pixels. The response uses the dimensions measured from the file. The minimum is 0. The maximum is 10,000. |
| `start_seconds` | number | No | This is the initial playback position for a video, in seconds. The playback position must not exceed the decoded video duration. The minimum is 0. |
| `supports_streaming` | boolean | No | This is true when this video is suitable for streaming playback. |
| `thumbnail` | [InputThumbnail](inputthumbnail.md) | No | This contains the thumbnail file or source associated with the media. |
| `type` | `"video"` | Yes | The content type is video. |
| `video` | [InputFile](inputfile.md) | Yes | This contains the video file or source for the content. |
| `width` | integer | No | You can supply a dimension hint in pixels. The response uses the dimensions measured from the file. The minimum is 0. The maximum is 10,000. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
