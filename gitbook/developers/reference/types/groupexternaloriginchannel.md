# GroupExternalOriginChannel

GroupExternalOriginChannel is a group contract model. Availability is controlled by the evaluated group feature flags.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `author_signature` | string | No | This is the signature displayed for the author of the original message. |
| `chat` | object | Yes | This identifies the chat associated with the message or event. |
| `chat.id` | string | Yes | The identifier of this object. The value must match `^[1-9][0-9]*$`. |
| `chat.kind` | `"channel"` | Yes | The chat, message or sender variant. |
| `chat.title` | string | No | This is the title displayed for the object. |
| `chat.username` | string | No | The public username of the group or channel, when assigned. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `sent_at` | string | Yes | This records when the message was sent, in RFC 3339 format. The format is `date-time`. |
| `type` | `"channel"` | Yes | The service or update type selecting this payload. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
