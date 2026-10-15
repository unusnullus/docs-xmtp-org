# InaccessibleMessage

The identity of a message whose content is unavailable to your bot.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat` | [Chat](chat.md) | Yes | This identifies the chat associated with the message or event. |
| `kind` | `"inaccessible"` | Yes | The value inaccessible identifies a message whose content your bot cannot read. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
