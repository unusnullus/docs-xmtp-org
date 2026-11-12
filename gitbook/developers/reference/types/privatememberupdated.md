# PrivateMemberUpdated

A private membership transition. Legacy payloads may omit chat.

The following condition must not hold: `chat` has the type object or `service_kind` is present.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `at` | string | Yes | This records the date and time of the event or displayed date, in RFC 3339 format. The format is `date-time`. |
| `new` | [ChatMember](chatmember.md) | Yes | This is the chat membership after the change. |
| `old` | [ChatMember](chatmember.md) | Yes | This is the chat membership before the change. |
| `user` | [UserIdentity](useridentity.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
