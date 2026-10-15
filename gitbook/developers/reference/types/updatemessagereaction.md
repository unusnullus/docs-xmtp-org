# UpdateMessageReaction

An update carrying a change to a message reaction.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | This records when the reaction change update was created, in RFC 3339 format. The format is `date-time`. |
| `message_reaction` | [ReactionUpdate](reactionupdate.md) | Yes | This describes a change to a user's reactions on a message. |
| `type` | `"message_reaction"` | Yes | The update type is message_reaction. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
