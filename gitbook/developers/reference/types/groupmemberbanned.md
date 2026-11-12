# GroupMemberBanned

GroupMemberBanned is a group contract model. Availability is controlled by the evaluated group feature flags.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `status` | `"banned"` | Yes | This is the membership status of the user in the chat. |
| `until_at` | string | No | The membership, restriction or ban expiration time in RFC 3339 format; omitted when indefinite. The format is `date-time`. |
| `user` | [UserIdentity](useridentity.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
