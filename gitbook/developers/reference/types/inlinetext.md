# InlineText

Text content with optional formatting, entities, and link preview choices.

Only the listed fields are allowed. Do not supply `format` and `entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `entities` | Array of [InputEntity](inputentity.md) | No | These define formatting with offsets and lengths measured in UTF-16 code units. In requests, use these instead of format. |
| `format` | `"plain"` or `"markdown"` or `"html"` | No | This specifies how to interpret or display the value. |
| `link_preview` | [InputLinkPreview](inputlinkpreview.md) | No | This contains link preview settings or the resulting preview metadata. |
| `text` | string | Yes | You can use up to 4,096 UTF-16 code units after markup is parsed. |
| `type` | `"text"` | Yes | The content type is text. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
