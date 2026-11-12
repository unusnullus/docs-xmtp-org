# GroupServiceMessage

GroupServiceMessage is a group contract model. Availability is controlled by the evaluated group feature flags.

The response omits `subject_user_id`, `chat_event_seq`, `prev_chat_event_seq`, `privacy_target_bot_id`, `operator_epoch`, and `callback_context`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `author_signature` | string | No | This is the signature displayed for the author of the original message. |
| `chat` | [GroupChat](groupchat.md) | Yes | This identifies the chat associated with the message or event. |
| `edited_at` | string | No | The time of the latest accepted edit in RFC 3339 format. The format is `date-time`. |
| `fallback_text` | string | No | This is a plain text representation of the message content. |
| `has_protected_content` | boolean | No | This is true when the message is protected against copying and forwarding. |
| `is_ephemeral` | `false` | No | An ordinary group message may omit this field or set it to false. Ephemeral content is outside this contract. |
| `kind` | `"service"` | Yes | The chat, message or sender variant. |
| `media_group_id` | string | No | This is the identifier shared by the messages in the same album. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `reply_markup` | [ReplyMarkup](replymarkup.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `reply_to` | [GroupReply](groupreply.md) | No | This identifies the reply target and optional quote for the message. |
| `revision` | string | No | The message revision, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `sender_chat` | [GroupSenderChat](groupsenderchat.md) | No | The real group or channel identity used by an anonymous sender or automatic forward. |
| `sender_tag` | string | No | The member tag displayed with the message. |
| `sender_user` | [User](user.md) | No | This identifies the user or bot that sent the message. |
| `sent_at` | string | Yes | This records when the message was sent, in RFC 3339 format. The format is `date-time`. |
| `service` | [GroupService](groupservice.md) | Yes | This contains the service event represented by the message. |
| `silent` | boolean | No | Set this to true to send or pin the message without a notification sound. |
| `state_version` | string | No | This version orders changes to the message's state and is encoded as a decimal string. The value must match `^[0-9]+$`. |
| `topic_id` | string | No | This is the topic ID within the chat, when the event belongs to a topic. The value must match `^[1-9][0-9]*$`. |
| `via_bot` | [User](user.md) | No | The bot through which inline content was submitted. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
