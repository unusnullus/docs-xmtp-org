# MediaFile

The file identifier and measured metadata for media embedded in a message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `cover` | [Photo](photo.md) | No | This is the cover image for the video. |
| `duration_seconds` | number | No | This is the duration of the media, in seconds. The minimum is 0. |
| `file_id` | string | Yes | Your bot can use this opaque identifier to send or retrieve the file. |
| `file_name` | string | No | This is the filename associated with the upload or returned file. |
| `file_size` | integer | Yes | This is the file size in bytes. The minimum is 0. |
| `file_unique_id` | string | Yes | This is the stable identifier for recognizing the same file. You cannot use it to download or send a file. |
| `height` | integer | No | This is the height of the media or layout, in pixels. The minimum is 0. |
| `length` | integer | No | This is the diameter of the round video note, in pixels. The minimum is 0. |
| `mime` | string | No | The detected MIME type of the media embedded in this message. |
| `mime_type` | string | No | The MIME type when file metadata uses the files.get representation. Embedded media uses mime instead. |
| `performer` | string | No | This identifies the performer associated with the audio file. You can use up to 256 UTF-16 code units after markup is parsed. |
| `start_seconds` | number | No | This is the initial playback position for a video, in seconds. The minimum is 0. |
| `supports_streaming` | boolean | No | This is true when this video is suitable for streaming playback. |
| `thumbnail` | [MediaFile](mediafile.md) | No | This contains the thumbnail file or source associated with the media. |
| `title` | string | No | This is the title displayed for the object. You can use up to 256 UTF-16 code units after markup is parsed. |
| `width` | integer | No | This is the width of the media or layout, in pixels. The minimum is 0. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
