# InputRemoveKeyboard

A request to remove the user's custom reply keyboard.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `selective` | boolean | No | This is true when the keyboard or reply prompt targets only the relevant users. |
| `type` | `"remove_keyboard"` | Yes | The keyboard type is remove_keyboard. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
