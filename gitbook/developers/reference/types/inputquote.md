# InputQuote

The text excerpt and position to quote from the message being replied to.

Only the listed fields are allowed. Do not supply `format` and `entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `entities` | Array of [InputEntity](inputentity.md) | No | These define formatting with offsets and lengths measured in UTF-16 code units. In requests, use these instead of format. |
| `format` | `"plain"` or `"markdown"` or `"html"` | No | Choose the markup format used to parse text. Use this instead of entities. |
| `position` | integer | No | This is the position of the quote in the original message, in UTF-16 code units. The minimum is 0. |
| `text` | string | Yes | The quoted excerpt of the message being replied to. You can quote up to 1,024 UTF-16 code units after markup is parsed. An empty quote is omitted from the message. The minimum length is 0 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
