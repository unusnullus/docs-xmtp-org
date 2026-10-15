# Quote

The quoted excerpt and its position in the original message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `entities` | Array of [Entity](entity.md) | No | These define formatting with offsets and lengths measured in UTF-16 code units. In requests, use these instead of format. |
| `format` | `"plain"` or `"markdown"` or `"html"` | No | The markup format associated with the quoted excerpt. |
| `is_manual` | boolean | No | This is true when the quote was supplied manually instead of matched to the original message. |
| `position` | integer | No | This is the position of the quote in the original message, in UTF-16 code units. The minimum is 0. |
| `text` | string | Yes | The quoted excerpt of the message being replied to. You can use up to 1,024 UTF-16 code units after markup is parsed. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
