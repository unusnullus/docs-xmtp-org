# GroupMessageReactionCount

Anonymous reaction totals, at most once per message per minute and with at most two minutes delay. Requires an administrator and explicit message_reaction_count opt-in.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `at` | string | Yes | This records the date and time of the event or displayed date, in RFC 3339 format. The format is `date-time`. |
| `chat` | [GroupChat](groupchat.md) | Yes | This identifies the chat associated with the message or event. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `reactions` | Array of object | Yes | This contains reactions to apply or the current reaction state. An empty input array removes your bot's reaction. |
| `reactions[].reaction` | [Reaction](reaction.md) | Yes | This is the reaction represented by the count. |
| `reactions[].total_count` | integer | Yes | This is the total number of items or reactions in this collection. The minimum is 0. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
