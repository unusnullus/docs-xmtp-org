# InputReplyKeyboard

A custom reply keyboard arranged in rows, with optional display settings.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `one_time` | boolean | No | This is true when the reply keyboard hides after a user presses a button. |
| `open_reply` | boolean | No | Whether to open the reply input when the keyboard appears. Once the message is sent, you cannot change this value by editing or replacing its inline keyboard. |
| `persistent` | boolean | No | This is true when the reply keyboard remains readily available to the user. |
| `placeholder` | string | No | This is the placeholder displayed in the message input field. You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `resize` | boolean | No | This is true when the client resizes the reply keyboard to fit its buttons. |
| `rows` | Array of Array of [InputReplyButton](inputreplybutton.md) | Yes | These are the rows of the keyboard or rich table, in display order. You can include up to 100 buttons across all rows. Set an inline keyboard rows array to [] to remove its buttons. The minimum is 1 item. |
| `selective` | boolean | No | This is true when the keyboard or reply prompt targets only the relevant users. |
| `type` | `"reply_keyboard"` | Yes | The keyboard type is reply_keyboard. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
