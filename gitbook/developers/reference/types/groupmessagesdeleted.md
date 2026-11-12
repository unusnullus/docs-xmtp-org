# GroupMessagesDeleted

GroupMessagesDeleted is a group contract model. Availability is controlled by the evaluated group feature flags.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat` | [GroupChat](groupchat.md) | Yes | This identifies the chat associated with the message or event. |
| `deleted_at` | string | Yes | This records when the message was deleted, in RFC 3339 format. The format is `date-time`. |
| `deleted_by_user` | [User](user.md) | No | The actor that deleted the messages, when disclosed. |
| `message_ids` | Array of string | Yes | These are message IDs within the specified chat, encoded as decimal strings. The minimum is 1 item. |
| `message_ids[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
