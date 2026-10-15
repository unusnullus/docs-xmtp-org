# InputText

The text message body and either a markup format or explicit formatting entities.

Only the listed fields are allowed. Do not supply `format` and `entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `entities` | Array of [InputEntity](inputentity.md) | No | These define formatting with offsets and lengths measured in UTF-16 code units. In requests, use these instead of format. |
| `format` | `"plain"` or `"markdown"` or `"html"` | No | Choose the markup format used to parse text. Use this instead of entities. |
| `text` | string | Yes | The message body. Formatting entities describe spans within this text. You can use up to 4,096 UTF-16 code units after markup is parsed. |
| `type` | `"text"` | Yes | The content type is text. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
