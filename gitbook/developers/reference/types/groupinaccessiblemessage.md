# GroupInaccessibleMessage

GroupInaccessibleMessage is a group contract model. Availability is controlled by the evaluated group feature flags.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat` | [GroupChat](groupchat.md) | Yes | This identifies the chat associated with the message or event. |
| `kind` | `"inaccessible"` | Yes | The chat, message or sender variant. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
