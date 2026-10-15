# ServiceMessage

A received message that reports a service event rather than ordinary content.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat` | [Chat](chat.md) | Yes | This identifies the chat associated with the message or event. |
| `effect_id` | string | No | Use a message effect identifier returned by message_effects.list. |
| `fallback_text` | string | No | This is a plain text representation of the message content. |
| `forward_origin` | [ForwardOrigin](forwardorigin.md) | No | This identifies the origin of a forwarded message. |
| `has_protected_content` | boolean | No | This is true when the message is protected against copying and forwarding. |
| `kind` | `"service"` | Yes | The value service identifies a message about a chat event. |
| `link_preview` | [LinkPreview](linkpreview.md) | No | This contains link preview settings or the resulting preview metadata. |
| `media_group_id` | string | No | This is the identifier shared by the messages in the same album. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `reactions` | [MessageReactions](messagereactions.md) | No | This contains reactions to apply or the current reaction state. An empty input array removes your bot's reaction. |
| `reply_markup` | [InlineKeyboard](inlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `reply_to` | [Reply](reply.md) | No | This identifies the reply target and optional quote for the message. |
| `revision` | string | No | This is the message's revision, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `sender_user` | [User](user.md) | Yes | This identifies the user or bot that sent the message. |
| `sent_at` | string | Yes | This records when the message was sent, in RFC 3339 format. The format is `date-time`. |
| `service` | [Service](service.md) | Yes | This contains the service event represented by the message. |
| `silent` | boolean | No | Set this to true to send or pin the message without a notification sound. |
| `start_parameter` | string | No | Your bot receives this payload when a user starts it through a deep link. |
| `state_version` | string | No | This version orders changes to the message's state and is encoded as a decimal string. The value must match `^[0-9]+$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
