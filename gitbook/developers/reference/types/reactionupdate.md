# ReactionUpdate

The previous and current reactions selected by a user on a message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `at` | string | Yes | This records the date and time of the event or displayed date, in RFC 3339 format. The format is `date-time`. |
| `chat` | [Chat](chat.md) | No | This identifies the chat associated with the message or event. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `new_reactions` | Array of [Reaction](reaction.md) | Yes | These are the user's reactions after the change. |
| `old_reactions` | Array of [Reaction](reaction.md) | Yes | These are the user's reactions before the change. |
| `user` | [User](user.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
