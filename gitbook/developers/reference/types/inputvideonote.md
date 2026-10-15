# InputVideoNote

The file source and message settings for sending a round video note.

Only the listed fields are allowed. Do not supply `caption_format` and `caption_entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `duration_seconds` | number | No | You can supply a duration hint in seconds. The response uses the duration measured from the file. The minimum is 0. The maximum is 86,400. |
| `length` | integer | No | You can supply a diameter hint in pixels. The response uses the width measured from the video file. The minimum is 0. The maximum is 10,000. |
| `thumbnail` | [InputThumbnail](inputthumbnail.md) | No | This contains the thumbnail file or source associated with the media. |
| `type` | `"video_note"` | Yes | The content type is video_note. |
| `video_note` | [InputFile](inputfile.md) | Yes | This contains the round video note file or source. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
