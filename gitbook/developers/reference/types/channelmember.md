# ChannelMember

A channel membership with its status and administrator rights.

If `status` is omitted or is `"owner"` or `"administrator"`, `is_anonymous` has the type `false`. If `status` is omitted or is `"administrator"`, the response includes `can_change_info`, `can_delete_messages`, `can_delete_stories`, `can_edit_messages`, `can_edit_stories`, `can_invite_users`, `can_manage_chat`, `can_manage_direct_messages`, `can_manage_tags`, `can_manage_topics`, `can_manage_video_chats`, `can_pin_messages`, `can_post_messages`, `can_post_stories`, `can_promote_members`, `can_restrict_members`, `can_send_welcome_messages`, `is_anonymous`, and `can_be_edited`.

You receive one of the following forms. Required fields apply within each form.

- `status` is `"owner"`: [GroupMemberOwner](groupmemberowner.md).

- `status` is `"administrator"`: [GroupMemberAdministrator](groupmemberadministrator.md).

- `status` is `"member"`: [GroupMemberMember](groupmembermember.md).

- `status` is `"left"`: [GroupMemberLeft](groupmemberleft.md).

- `status` is `"banned"`: [GroupMemberBanned](groupmemberbanned.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
