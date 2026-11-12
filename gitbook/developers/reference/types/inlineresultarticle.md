# InlineResultArticle

An inline article result with an ID chosen by your bot.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `content` | [InlineMessageContent](inlinemessagecontent.md) | Yes | This contains the message content. The type field selects its variant. |
| `description` | string | No | This is the description displayed for the object. |
| `id` | string | Yes | The identifier of this object. The minimum length is 1 character. The maximum length is 64 characters. |
| `reply_markup` | [InputInlineKeyboard](inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `thumbnail` | object | No | This contains the thumbnail file or source associated with the media. |
| `title` | string | Yes | This is the title displayed for the object. |
| `type` | `"article"` | Yes | The content type is article. |
| `url` | string | No | The HTTPS URL used for this result. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
