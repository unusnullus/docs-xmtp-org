# ForwardOrigin

The original sender and source message attribution retained by a forwarded message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `author_signature` | string | No | This is the signature displayed for the author of the original message. |
| `chat` | [Chat](chat.md) | No | This identifies the chat associated with the message or event. |
| `message_id` | string | No | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `sender_name` | string | No | This is the display name retained when the original sender's identity is hidden. |
| `sent_at` | string | No | This records when the message was sent, in RFC 3339 format. The format is `date-time`. |
| `type` | `"user"` or `"hidden_user"` or `"chat"` or `"channel"` | Yes | The source attribution kind: user, hidden_user, chat, or channel. |
| `user` | [User](user.md) | No | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
