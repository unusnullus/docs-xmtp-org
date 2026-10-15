# MessageEffect

One visual message effect available for the listed chat kinds.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `asset_version` | string | Yes | This is the version of the visual assets for this message effect. |
| `available_in` | Array of `"private"` | Yes | You can use this message effect in these chat kinds. |
| `effect_id` | string | Yes | Use a message effect identifier returned by message_effects.list. |
| `emoji` | string | Yes | This is the emoji used for the reaction, dice, or effect. |
| `title` | string | Yes | This is the title displayed for the object. You can use up to 64 UTF-16 code units after markup is parsed. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
