# GroupMemberUpdated

A membership transition. user is the actor, while old.user and new.user identify the subject. Already routed updates survive a later removal; subsequent bot operations require current membership.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `at` | string | Yes | This records the date and time of the event or displayed date, in RFC 3339 format. The format is `date-time`. |
| `chat` | [GroupChat](groupchat.md) | Yes | This identifies the chat associated with the message or event. |
| `invite_link` | [GroupInviteLink](groupinvitelink.md) | No | An invitation available in this context. A bot can manage only invitations it created. |
| `new` | [GroupMember](groupmember.md) | Yes | This is the chat membership after the change. |
| `old` | [GroupMember](groupmember.md) | Yes | Previous public membership. A user who was not a member is represented with status left, the same user and no restriction fields. |
| `user` | [UserIdentity](useridentity.md) | Yes | This identifies the user associated with the event, mention, or private chat. |
| `via_join_request` | boolean | No | Whether the member joined through an approved join request. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
