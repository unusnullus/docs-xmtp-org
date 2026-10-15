# File

A file upload result or metadata returned by files.get. Save file_id to reuse or retrieve the file.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `cover` | [Photo](photo.md) | No | This is the cover image for the video. |
| `download_url` | string | No | You can use this temporary URL to download the original file. The format is `uri`. |
| `download_url_expires_at` | string | No | This records when the temporary download URL expires, in RFC 3339 format. The format is `date-time`. |
| `duration_seconds` | number | No | This is the duration of the media, in seconds. The minimum is 0. |
| `file_id` | string | Yes | Your bot can use this opaque identifier to send or retrieve the file. |
| `file_name` | string | No | This is the filename associated with the upload or returned file. |
| `file_size` | integer | Yes | This is the file size in bytes. The minimum is 0. |
| `file_unique_id` | string | Yes | This is the stable identifier for recognizing the same file. You cannot use it to download or send a file. |
| `height` | integer | No | This is the height of the media or layout, in pixels. The minimum is 0. |
| `kind` | `"photo"` or `"video"` or `"animation"` or `"audio"` or `"voice"` or `"video_note"` or `"document"` or `"live_photo_video"` or `"thumbnail"` | Yes | This identifies the file's media kind, such as photo, video, audio, or document. |
| `length` | integer | No | This is the diameter of the round video note, in pixels. The minimum is 0. |
| `mime` | string | No | The MIME type used in embedded media metadata. For the files.get result, read mime_type instead. |
| `mime_type` | string | No | The detected MIME type returned by files.get. This is exposed as mime in embedded media metadata. |
| `performer` | string | No | This identifies the performer associated with the audio file. You can use up to 256 UTF-16 code units after markup is parsed. |
| `start_seconds` | number | No | This is the initial playback position for a video, in seconds. The minimum is 0. |
| `state` | `"uploading"` or `"processing"` or `"ready"` | Yes | This is the current processing state of the file or link preview. |
| `supports_streaming` | boolean | No | This is true when this video is suitable for streaming playback. |
| `thumbnail` | [MediaFile](mediafile.md) | No | This contains the thumbnail file or source associated with the media. |
| `title` | string | No | This is the title displayed for the object. You can use up to 256 UTF-16 code units after markup is parsed. |
| `width` | integer | No | This is the width of the media or layout, in pixels. The minimum is 0. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
