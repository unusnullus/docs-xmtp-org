# MessagesDeleted

The messages reported as deleted from one chat.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat` | [Chat](chat.md) | No | This identifies the chat associated with the message or event. |
| `message_ids` | Array of string | Yes | These are message IDs within the specified chat, encoded as decimal strings. |
| `message_ids[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
