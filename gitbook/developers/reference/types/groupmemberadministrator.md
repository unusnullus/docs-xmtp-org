# GroupMemberAdministrator

GroupMemberAdministrator is a group contract model. Availability is controlled by the evaluated group feature flags.

If `user` is omitted or has the type object, `is_anonymous` has the type `false`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `can_be_edited` | boolean | Yes | Whether the be edited permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_change_info` | boolean | Yes | The change_info right. Effective authority depends on current membership, hierarchy and product support. |
| `can_delete_messages` | boolean | Yes | The delete_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `can_delete_stories` | boolean | Yes | The delete_stories right. Effective authority depends on current membership, hierarchy and product support. |
| `can_edit_messages` | boolean | No | The edit_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `can_edit_stories` | boolean | Yes | The edit_stories right. Effective authority depends on current membership, hierarchy and product support. |
| `can_invite_users` | boolean | Yes | The invite_users right. Effective authority depends on current membership, hierarchy and product support. |
| `can_manage_chat` | boolean | Yes | The manage_chat right. Effective authority depends on current membership, hierarchy and product support. |
| `can_manage_direct_messages` | boolean | No | The manage_direct_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `can_manage_tags` | boolean | No | The manage_tags right. Effective authority depends on current membership, hierarchy and product support. |
| `can_manage_topics` | boolean | No | The manage_topics right. Effective authority depends on current membership, hierarchy and product support. |
| `can_manage_video_chats` | boolean | Yes | The manage_video_chats right. Effective authority depends on current membership, hierarchy and product support. |
| `can_pin_messages` | boolean | No | The pin_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `can_post_messages` | boolean | No | The post_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `can_post_stories` | boolean | Yes | The post_stories right. Effective authority depends on current membership, hierarchy and product support. |
| `can_promote_members` | boolean | Yes | The promote_members right. Effective authority depends on current membership, hierarchy and product support. |
| `can_restrict_members` | boolean | Yes | The restrict_members right. Effective authority depends on current membership, hierarchy and product support. |
| `can_send_welcome_messages` | boolean | Yes | The send_welcome_messages right. Effective authority depends on current membership, hierarchy and product support. |
| `custom_title` | string | No | The administrator's displayed title, up to 16 characters. |
| `is_anonymous` | boolean | Yes | Whether this administrator sends as the group identity. Bot administrators cannot be anonymous. |
| `status` | `"administrator"` | Yes | This is the membership status of the user in the chat. |
| `user` | [UserIdentity](useridentity.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
