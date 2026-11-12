# InlineResultContact

An inline contact result with an ID chosen by your bot.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `contact` | object | Yes | This is the contact card shared in the message. |
| `content` | [InlineMessageContent](inlinemessagecontent.md) | No | This contains the message content. The type field selects its variant. |
| `id` | string | Yes | The identifier of this object. The minimum length is 1 character. The maximum length is 64 characters. |
| `reply_markup` | [InputInlineKeyboard](inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `thumbnail` | object | No | This contains the thumbnail file or source associated with the media. |
| `type` | `"contact"` | Yes | The content type is contact. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
