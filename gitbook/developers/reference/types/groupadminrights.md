# GroupAdminRights

Granular group administrator rights. Missing values grant no right. Unsupported product rights may be stored/delegated but have no effect; only the server decides effective permissions and promotion ancestry.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `can_change_info` | boolean | No | The change_info right. Effective authority depends on current membership, hierarchy and product support. |
| `can_delete_messages` | boolean | No | The delete_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `can_delete_stories` | boolean | No | The delete_stories right. Effective authority depends on current membership, hierarchy and product support. |
| `can_edit_messages` | boolean | No | The edit_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `can_edit_stories` | boolean | No | The edit_stories right. Effective authority depends on current membership, hierarchy and product support. |
| `can_invite_users` | boolean | No | The invite_users right. Effective authority depends on current membership, hierarchy and product support. |
| `can_manage_chat` | boolean | No | The manage_chat right. Effective authority depends on current membership, hierarchy and product support. |
| `can_manage_direct_messages` | boolean | No | The manage_direct_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `can_manage_tags` | boolean | No | The manage_tags right. Effective authority depends on current membership, hierarchy and product support. |
| `can_manage_topics` | boolean | No | The manage_topics right. Effective authority depends on current membership, hierarchy and product support. |
| `can_manage_video_chats` | boolean | No | The manage_video_chats right. Effective authority depends on current membership, hierarchy and product support. |
| `can_pin_messages` | boolean | No | The pin_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `can_post_messages` | boolean | No | The post_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `can_post_stories` | boolean | No | The post_stories right. Effective authority depends on current membership, hierarchy and product support. |
| `can_promote_members` | boolean | No | The promote_members right. Effective authority depends on current membership, hierarchy and product support. |
| `can_restrict_members` | boolean | No | The restrict_members right. Effective authority depends on current membership, hierarchy and product support. |
| `can_send_welcome_messages` | boolean | No | The send_welcome_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `is_anonymous` | boolean | No | Whether the administrator acts as the group identity. Bot administrators cannot be anonymous. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
