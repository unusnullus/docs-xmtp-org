# ChannelChatFullInfo

Current public channel FullInfo for a bot member or administrator. A non-member, private, missing or blocked channel returns chat_not_found. Optional pinned_message is the latest visible pinned post by send time, not pin time. No private identifiers or membership authority are exposed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `description` | string | No | This is the description displayed for the object. The maximum length is 255 characters. |
| `has_protected_content` | boolean | Yes | This is true when the message is protected against copying and forwarding. |
| `id` | string | Yes | The identifier of this object. The value must match `^[1-9][0-9]*$`. |
| `is_forum` | `false` | Yes | This is true when the chat has forum topics. |
| `kind` | `"channel"` | Yes | This context belongs to a public channel. |
| `pinned_message` | [ContentMessage](contentmessage.md) | No | The latest visible pinned channel post by send time, not pin time. Omitted when no visible pinned post is available. |
| `signatures_enabled` | boolean | Yes | This is true when signatures are enabled for posts in this channel. |
| `title` | string | Yes | This is the title displayed for the object. The minimum length is 1 character. The maximum length is 128 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
