# InlineResultGame

An inline game result with an ID chosen by your bot.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `game_short_name` | string | Yes | The short name of an active game owned by your bot. |
| `id` | string | Yes | The identifier of this object. The minimum length is 1 character. The maximum length is 64 characters. |
| `reply_markup` | [InputInlineKeyboard](inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `type` | `"game"` | Yes | The content type is game. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
