# PrivateChat

The private chat identity and its user. Read your bot's existing private chat with this user. The chat ID differs from the user ID. This request does not create a chat. An unknown chat or a chat outside your bot's scope returns chat_not_found.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `id` | string | Yes | This is the chat ID, encoded as a decimal string. It differs from the user's ID. The value must match `^[1-9][0-9]*$`. |
| `is_forum` | boolean | Yes | This is true when the chat has forum topics. |
| `kind` | `"private"` | Yes | This chat is private: it connects your bot with one user. |
| `user` | object | Yes | This identifies the user associated with the event, mention, or private chat. |
| `user.has_photo` | boolean | Yes | This is true when this user currently has a profile photo. |
| `user.id` | string | Yes | This is the user ID of the account in the private chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `user.is_bot` | `false` | Yes | This is true when this account is a bot. |
| `user.name` | string | Yes | This is the display name of the user, bot, contact, or provider. |
| `user.username` | string | Yes | This is the handle of the user or bot. The value may be absent or null when no handle is available. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
