# InputReplyButtonText

A reply button that sends its label as a text message.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `text` | string | Yes | The label displayed on this button. You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `type` | `"text"` | Yes | The button action type is text. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
