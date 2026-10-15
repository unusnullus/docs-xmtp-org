# InputRichInlineButtonCopyText

A button that copies its configured text to the user's clipboard. Use this node when sending rich content.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `copy` | string | Yes | The client copies this text to the clipboard when the user presses the button. You can use up to 256 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `text` | string or [RichButtonDateTime](richbuttondatetime.md) or Array of [RichButtonLabelPart](richbuttonlabelpart.md) | Yes | The label displayed on this button. Use plain strings, date_time nodes, or arrays of these values. You can nest arrays to a depth of 3. The complete label must contain 1 to 64 UTF-16 code units, although individual array segments may be empty. |
| `type` | `"copy_text"` | Yes | The button action type is copy_text. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
