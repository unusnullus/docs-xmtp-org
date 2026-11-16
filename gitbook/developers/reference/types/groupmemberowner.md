# GroupMemberOwner

GroupMemberOwner describes a public group payload.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `custom_title` | string | No | The administrator's displayed title, up to 16 characters. |
| `is_anonymous` | boolean | Yes | Whether this administrator sends as the group identity. Bot administrators cannot be anonymous. |
| `status` | `"owner"` | Yes | This is the membership status of the user in the chat. |
| `user` | [UserIdentity](useridentity.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
