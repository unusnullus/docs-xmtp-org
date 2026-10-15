# Pins

The pinned message IDs returned for a chat.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_id` | string | Yes | This is the chat ID, encoded as a decimal string. A private chat ID differs from the user's ID. The value must match `^[1-9][0-9]*$`. |
| `pinned_message_ids` | Array of string | Yes | These are the IDs of the messages currently pinned in the chat. |
| `pinned_message_ids[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |
| `pins_version` | string | Yes | This is the version of the chat's pinned message list, encoded as a decimal string. The value must match `^[0-9]+$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
