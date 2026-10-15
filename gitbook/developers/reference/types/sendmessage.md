# SendMessage

The content and delivery settings for a new message.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `content` | [InputContent](inputcontent.md) | Yes | This contains the message content. The type field selects its variant. |
| `effect_id` | string | No | Use a message effect identifier returned by message_effects.list. The value must match `^[A-Za-z0-9_-]{1,64}$`. |
| `link_preview` | [InputLinkPreview](inputlinkpreview.md) or null | No | This contains link preview settings or the resulting preview metadata. |
| `protect_content` | boolean | No | Set this to true to protect the new message against copying and forwarding. |
| `reply_markup` | [ReplyMarkup](replymarkup.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `reply_to` | [InputReply](inputreply.md) | No | This identifies the reply target and optional quote for the message. |
| `silent` | boolean | No | Set this to true to send or pin the message without a notification sound. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
