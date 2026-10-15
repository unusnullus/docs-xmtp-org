# UploadFile

The file kind and optional filename sent in the JSON payload part of a file upload.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `file_name` | string | No | This is the filename associated with the upload or returned file. The maximum length is 1,024 characters. |
| `kind` | `"photo"` or `"video"` or `"animation"` or `"audio"` or `"voice"` or `"video_note"` or `"document"` or `"live_photo_video"` | Yes | Choose how to use the uploaded file, such as photo, video, audio, or document. The bytes must match the selected kind. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
