# SetReactions

The reaction to apply to a message. An empty list removes your bot's reaction. When you react to an album item, the first surviving visible item is the target. If it is deleted, the next item becomes the target. Repeating the current reaction leaves the message unchanged. An idempotent replay returns the original result.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `is_big` | boolean | No | Set this to true to display a larger reaction animation. |
| `reactions` | Array of [Reaction](reaction.md) | Yes | This contains reactions to apply or the current reaction state. An empty input array removes your bot's reaction. The maximum is 1 item. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
