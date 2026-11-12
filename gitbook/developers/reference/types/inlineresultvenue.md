# InlineResultVenue

An inline venue result with an ID chosen by your bot.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `content` | [InlineMessageContent](inlinemessagecontent.md) | No | This contains the message content. The type field selects its variant. |
| `id` | string | Yes | The identifier of this object. The minimum length is 1 character. The maximum length is 64 characters. |
| `reply_markup` | [InputInlineKeyboard](inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `thumbnail` | object | No | This contains the thumbnail file or source associated with the media. |
| `type` | `"venue"` | Yes | The content type is venue. |
| `venue` | object | Yes | This contains the venue name, address, coordinates, and optional provider information. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
