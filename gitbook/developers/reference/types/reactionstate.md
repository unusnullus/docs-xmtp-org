# ReactionState

The current reactions and state version returned after setting a message reaction.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `reactions` | [MessageReactions](messagereactions.md) | Yes | This contains reactions to apply or the current reaction state. An empty input array removes your bot's reaction. |
| `state_version` | string | Yes | This version orders changes to the message's state and is encoded as a decimal string. The value must match `^[0-9]+$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
