# AdminRights

The administrator permissions requested by your bot when it is added to a chat. These defaults do not grant permissions. These are the default permissions your bot requests when added to a chat. They do not grant access. For groups, can_post_messages and can_edit_messages must be false. Unsupported permissions must also be false. Set rights to null to reset the defaults.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `can_change_info` | boolean | No | This is true when your bot requests permission to change chat information. |
| `can_delete_messages` | boolean | No | This is true when your bot requests permission to delete messages. |
| `can_delete_stories` | boolean | No | Set this field to false or omit it. A true value returns capability_not_available. |
| `can_edit_messages` | boolean | No | This is true when your bot requests permission to edit channel messages. |
| `can_edit_stories` | boolean | No | Set this field to false or omit it. A true value returns capability_not_available. |
| `can_invite_users` | boolean | No | This is true when your bot requests permission to invite users. |
| `can_manage_chat` | boolean | No | This requests permission to manage the chat. The API sets it to true when you request any other permission except is_anonymous. |
| `can_manage_direct_messages` | boolean | No | Set this field to false or omit it. A true value returns capability_not_available. |
| `can_manage_tags` | boolean | No | This is true when your bot requests permission to manage member tags. |
| `can_manage_topics` | boolean | No | This is true when your bot requests permission to manage forum topics. |
| `can_manage_video_chats` | boolean | No | Set this field to false or omit it. A true value returns capability_not_available. |
| `can_pin_messages` | boolean | No | This is true when your bot requests permission to pin messages. |
| `can_post_messages` | boolean | No | This is true when your bot requests permission to post channel messages. |
| `can_post_stories` | boolean | No | Set this field to false or omit it. A true value returns capability_not_available. |
| `can_promote_members` | boolean | No | This is true when your bot requests permission to promote other members. |
| `can_restrict_members` | boolean | No | This is true when your bot requests permission to restrict members. |
| `can_send_welcome_messages` | boolean | No | This is true when your bot requests permission to send welcome messages. |
| `is_anonymous` | boolean | No | Set this to true to request permission for your bot to act as an anonymous administrator. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
