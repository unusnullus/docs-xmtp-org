# RichInlineButtonCallback

A button that sends a callback query to your bot.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `data` | string | Yes | Your bot receives this callback data when the user presses the button. You can use up to 64 UTF-8 bytes. The minimum length is 1 character. |
| `style` | `"primary"` or `"success"` or `"danger"` or `"link"` | No | This selects the visual style of the button. |
| `text` | string or [RichButtonDateTime](richbuttondatetime.md) or Array of [RichButtonLabelPart](richbuttonlabelpart.md) | Yes | The label displayed on this button. Use plain strings, date_time nodes, or arrays of these values. You can nest arrays to a depth of 3. The complete label must contain 1 to 64 UTF-16 code units, although individual array segments may be empty. |
| `type` | `"callback"` | Yes | The button action type is callback. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
