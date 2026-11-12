# SavePreparedKeyboardButton

A button bound to a known human account and this bot.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `button` | [PreparedUsersButton](preparedusersbutton.md) or [PreparedManagedButton](preparedmanagedbutton.md) | Yes | This defines an interactive button embedded in rich text. |
| `user_id` | string | Yes | This is the user ID, encoded as a decimal string. A user ID differs from a private chat ID. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
