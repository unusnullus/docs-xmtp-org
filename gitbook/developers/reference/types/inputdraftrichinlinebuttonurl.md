# InputDraftRichInlineButtonUrl

A button that opens a link. Use this node in a temporary rich draft.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `text` | string or [RichButtonDateTime](richbuttondatetime.md) or Array of [RichButtonLabelPart](richbuttonlabelpart.md) | Yes | The label displayed on this button. Use plain strings, date_time nodes, or arrays of these values. You can nest arrays to a depth of 3. The complete label must contain 1 to 64 UTF-16 code units, although individual array segments may be empty. |
| `type` | `"url"` | Yes | The button action type is url. |
| `url` | string | Yes | The link opened when the user activates this button or text link. The format is `uri`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
