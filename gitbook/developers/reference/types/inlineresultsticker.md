# InlineResultSticker

An inline sticker result with an ID chosen by your bot.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `id` | string | Yes | The identifier of this object. The minimum length is 1 character. The maximum length is 64 characters. |
| `media` | [InlineCachedFile](inlinecachedfile.md) | Yes | This contains attached media or the named media sources for a rich message. |
| `reply_markup` | [InputInlineKeyboard](inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `type` | `"sticker"` | Yes | The content type is sticker. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
