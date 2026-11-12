# GroupChatFullInfo

Group metadata available to a member bot. invite_link is the bot's own primary link. has_visible_history and has_aggressive_anti_spam_enabled are administrator-only. Missing available_reactions means all supported reactions; protected content cannot be copied or forwarded.

The response omits `bot`, `user`, `operator_epoch`, and `started`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `available_reactions` | Array of [Reaction](reaction.md) | No | The reaction types allowed by this group. |
| `description` | string | No | This is the description displayed for the object. The maximum length is 255 characters. |
| `has_aggressive_anti_spam_enabled` | boolean | No | Whether aggressive anti-spam moderation is enabled. |
| `has_hidden_members` | boolean | No | Whether the group hides its member list from ordinary members. |
| `has_protected_content` | boolean | No | This is true when the message is protected against copying and forwarding. |
| `has_visible_history` | boolean | No | Whether newly admitted members can see earlier group history. |
| `id` | string | Yes | The identifier of this object. The value must match `^[1-9][0-9]*$`. |
| `invite_link` | string | No | An invitation available in this context. A bot can manage only invitations it created. The format is `uri`. |
| `is_forum` | boolean | Yes | This is true when the chat has forum topics. |
| `join_by_request` | boolean | No | Whether a join request needs an administrator's approval. |
| `join_to_send_messages` | boolean | No | Whether membership is required to send to the group. |
| `kind` | `"group"` | Yes | The chat, message or sender variant. |
| `linked_chat_id` | string | No | The linked channel's decimal directory chat ID, when available. The value must match `^[1-9][0-9]*$`. |
| `max_reaction_count` | integer | No | The maximum number of distinct reactions a member can place on one message. The minimum is 1. The maximum is 11. |
| `message_auto_delete_seconds` | integer | No | The auto-delete interval for eligible new messages, in seconds; zero disables it. The minimum is 0. |
| `permissions` | [GroupPermissions](grouppermissions.md) | No | The group defaults or member permission values. Effective member permissions intersect the applicable group defaults. |
| `photo` | object | No | This contains the photo sizes or photo source for the object. |
| `photo.big_file_id` | string | Yes | The bot-scoped file ID for the large group photo. |
| `photo.big_file_unique_id` | string | Yes | The stable identity of the large group photo; it cannot be used for download. |
| `photo.small_file_id` | string | Yes | The bot-scoped file ID for the small group photo. |
| `photo.small_file_unique_id` | string | Yes | The stable identity of the small group photo; it cannot be used for download. |
| `pinned_message` | [GroupMessage](groupmessage.md) | No | The pinned group message visible to this bot. |
| `slow_mode_delay_seconds` | integer | No | The minimum interval between ordinary member sends, in seconds; zero disables slow mode. The minimum is 0. |
| `title` | string | Yes | This is the title displayed for the object. The minimum length is 1 character. The maximum length is 128 characters. |
| `username` | string | No | The group's public username, when one is assigned. The value must match `^[A-Za-z0-9]{8,24}$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
