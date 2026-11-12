# InputRichInlineButtonMiniApp

A rich-content Mini App button with a formatted label and a registered origin.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `text` | string or [RichButtonDateTime](richbuttondatetime.md) or Array of [RichButtonLabelPart](richbuttonlabelpart.md) | Yes | Use plain strings, date_time nodes, or arrays of these values. You can nest arrays to a depth of 3. The complete label must contain 1 to 64 UTF-16 code units, although individual array segments may be empty. |
| `type` | `"mini_app"` | Yes | The button action type is mini_app. |
| `url` | string | Yes | A Mini App URL on a registered, unblocked origin of this bot. Available only when Mini Apps are enabled. The format is `uri`. The maximum length is 2,048 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
