# Text

The message body after markup has been parsed, with its formatting entities.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `entities` | Array of [Entity](entity.md) | Yes | These define formatting with offsets and lengths measured in UTF-16 code units. In requests, use these instead of format. |
| `text` | string | Yes | The message body. Formatting entities describe spans within this text. You can use up to 4,096 UTF-16 code units after markup is parsed. |
| `type` | `"text"` | Yes | The content type is text. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
