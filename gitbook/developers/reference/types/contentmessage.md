# ContentMessage

A received message containing text, media, or another supported content variant.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat` | [Chat](chat.md) | Yes | This identifies the chat associated with the message or event. |
| `content` | [Content](content.md) | Yes | This contains the message content. The type field selects its variant. |
| `effect_id` | string | No | The identifier of the message effect attached to this message. |
| `fallback_text` | string | No | This is a plain text representation of the message content. |
| `forward_origin` | [ForwardOrigin](forwardorigin.md) | No | This identifies the origin of a forwarded message. |
| `has_protected_content` | boolean | No | This is true when the message is protected against copying and forwarding. |
| `kind` | `"content"` | Yes | The value content identifies a message with text, media, or other user-visible content. |
| `link_preview` | [LinkPreview](linkpreview.md) | No | This contains link preview settings or the resulting preview metadata. |
| `media_group_id` | string | No | This is the identifier shared by the messages in the same album. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `reactions` | [MessageReactions](messagereactions.md) | No | The message's current reactions, including counts and your bot's selected reaction when present. |
| `reply_markup` | [InlineKeyboard](inlinekeyboard.md) | No | The inline keyboard attached to this message. |
| `reply_to` | [Reply](reply.md) | No | This identifies the reply target and optional quote for the message. |
| `revision` | string | No | This is the message's revision, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `sender_user` | [User](user.md) | Yes | This identifies the user or bot that sent the message. |
| `sent_at` | string | Yes | This records when the message was sent, in RFC 3339 format. The format is `date-time`. |
| `silent` | boolean | No | Whether this message was sent without a notification sound. |
| `start_parameter` | string | No | Your bot receives this payload when a user starts it through a deep link. |
| `state_version` | string | No | This version orders changes to the message's state and is encoded as a decimal string. The value must match `^[0-9]+$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
