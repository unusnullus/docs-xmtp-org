# InputInlineButtonCopyText

A button that copies its configured text to the user's clipboard.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `copy` | string | Yes | The client copies this text to the clipboard when the user presses the button. You can use up to 256 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `text` | string | Yes | The label displayed on this button. You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `type` | `"copy_text"` | Yes | The button action type is copy_text. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
