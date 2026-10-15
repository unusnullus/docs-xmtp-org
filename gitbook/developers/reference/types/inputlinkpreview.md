# InputLinkPreview

The link preview options to apply to a text message.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `disabled` | boolean | No | This is true when the link preview is disabled. |
| `prefer_large_media` | boolean | No | Set this to true to prefer a large image in the link preview. |
| `prefer_small_media` | boolean | No | Set this to true to prefer a small image in the link preview. |
| `show_above_text` | boolean | No | This is true when the link preview appears above the message text. |
| `url` | string | No | Supply the URL to preview instead of the first eligible URL in the text. The format is `uri`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
