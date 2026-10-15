# Chat

The identity and kind of a chat available to your bot.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `id` | string | Yes | This is the chat ID, encoded as a decimal string. It differs from the user's ID. The value must match `^[1-9][0-9]*$`. |
| `kind` | `"private"` | Yes | This chat is private: it connects your bot with one user. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
