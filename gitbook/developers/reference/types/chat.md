# Chat

The identity and kind of a chat available to your bot.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `id` | string | Yes | This is the chat ID, encoded as a decimal string. It differs from the user's ID. The value must match `^[1-9][0-9]*$`. |
| `kind` | `"private"` or `"channel"` | Yes | The chat kind: private for a conversation with one user, or channel for a public channel. |
| `title` | string | No | This is the title displayed for the object. The minimum length is 1 character. The maximum length is 128 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
