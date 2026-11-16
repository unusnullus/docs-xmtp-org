# GroupMemberLeft

GroupMemberLeft describes a public group payload.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `status` | `"left"` | Yes | This is the membership status of the user in the chat. |
| `user` | [UserIdentity](useridentity.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
