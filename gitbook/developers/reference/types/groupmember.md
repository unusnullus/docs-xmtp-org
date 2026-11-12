# GroupMember

An authoritative membership variant. Restricted permissions intersect group defaults. Administrator rights do not authorize delegation outside the actor's promotion chain. Bot accounts cannot be owners or anonymous administrators.

You receive one of the following forms. Required fields apply within each form.

- `status` is `"owner"`: [GroupMemberOwner](groupmemberowner.md).

- `status` is `"administrator"`: [GroupMemberAdministrator](groupmemberadministrator.md).

- `status` is `"member"`: [GroupMemberMember](groupmembermember.md).

- `status` is `"restricted"`: [GroupMemberRestricted](groupmemberrestricted.md).

- `status` is `"left"`: [GroupMemberLeft](groupmemberleft.md).

- `status` is `"banned"`: [GroupMemberBanned](groupmemberbanned.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
