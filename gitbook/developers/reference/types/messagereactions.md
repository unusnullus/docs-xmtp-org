# MessageReactions

The current reaction counts and selected reaction for a message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `counts` | Array of object | Yes | These are the reaction totals grouped by reaction. |
| `counts[].reaction` | [Reaction](reaction.md) | Yes | This is the reaction represented by the count. |
| `counts[].total_count` | integer | Yes | This is the total number of items or reactions in this collection. The minimum is 0. |
| `is_big` | boolean | Yes | Set this to true to display a larger reaction animation. |
| `my_reactions` | Array of [Reaction](reaction.md) | Yes | These are the reactions that your bot has set on this message. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
