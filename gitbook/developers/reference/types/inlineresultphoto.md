# InlineResultPhoto

An inline photo result with an ID chosen by your bot.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [Entity](entity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `content` | [InlineMessageContent](inlinemessagecontent.md) | No | This contains the message content. The type field selects its variant. |
| `description` | string | No | This is the description displayed for the object. |
| `has_spoiler` | boolean | No | This is true when the media is hidden behind a spoiler. |
| `height` | integer | No | This is the height of the media or layout, in pixels. The minimum is 0. |
| `id` | string | Yes | The identifier of this object. The minimum length is 1 character. The maximum length is 64 characters. |
| `media` | [InlineInputFile](inlineinputfile.md) | Yes | This contains attached media or the named media sources for a rich message. |
| `reply_markup` | [InputInlineKeyboard](inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `thumbnail` | object | No | This contains the thumbnail file or source associated with the media. |
| `title` | string | No | This is the title displayed for the object. |
| `type` | `"photo"` | Yes | The content type is photo. |
| `width` | integer | No | This is the width of the media or layout, in pixels. The minimum is 0. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
