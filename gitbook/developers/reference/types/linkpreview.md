# LinkPreview

The link preview metadata and display settings returned with a message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `description` | string | No | This is the description displayed for the object. |
| `disabled` | boolean | No | This is true when the link preview is disabled. |
| `photo` | [Photo](photo.md) | No | This contains the photo sizes or photo source for the object. |
| `prefer_large_media` | boolean | No | Set this to true to prefer a large image in the link preview. |
| `prefer_small_media` | boolean | No | Set this to true to prefer a small image in the link preview. |
| `show_above_text` | boolean | No | This is true when the link preview appears above the message text. |
| `site_name` | string | No | This is the name of the website shown in the link preview. |
| `state` | string | No | This is the current processing state of the file or link preview. |
| `title` | string | No | This is the title displayed for the object. |
| `url` | string | No | The URL whose preview metadata is returned with this message. The format is `uri`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
