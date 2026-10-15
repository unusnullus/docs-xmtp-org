# InputAudio

The file source and message settings for sending an audio file.

Only the listed fields are allowed. Do not supply `caption_format` and `caption_entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `audio` | [InputFile](inputfile.md) | Yes | This contains the audio file or source for the content. |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_entities` | Array of [InputEntity](inputentity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `duration_seconds` | number | No | You can supply a duration hint in seconds. The response uses the duration measured from the file. The minimum is 0. The maximum is 86,400. |
| `performer` | string | No | This identifies the performer associated with the audio file. You can use up to 256 UTF-16 code units after markup is parsed. |
| `thumbnail` | [InputThumbnail](inputthumbnail.md) | No | This contains the thumbnail file or source associated with the media. |
| `title` | string | No | This is the title displayed for the object. You can use up to 256 UTF-16 code units after markup is parsed. |
| `type` | `"audio"` | Yes | The content type is audio. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
