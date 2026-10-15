# InputInlineKeyboard

An inline keyboard to attach to a message. Send empty rows to remove existing buttons.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `open_reply` | boolean | No | Whether to open the reply input when the keyboard appears. Once the message is sent, you cannot change this value by editing or replacing its inline keyboard. |
| `rows` | Array of Array of [InputInlineButton](inputinlinebutton.md) | Yes | These are the rows of the keyboard or rich table, in display order. You can include up to 100 buttons across all rows. Set an inline keyboard rows array to [] to remove its buttons. The minimum is 0 items. |
| `type` | `"inline_keyboard"` | Yes | The keyboard type is inline_keyboard. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
