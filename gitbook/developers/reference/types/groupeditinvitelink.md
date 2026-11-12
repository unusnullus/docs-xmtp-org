# GroupEditInviteLink

Arguments for invite_links.edit in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation.

Only the listed fields are allowed. Do not supply `member_limit` and `creates_join_request` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `creates_join_request` | boolean | No | Whether this invitation creates join requests. It cannot be combined with a member limit. The default is `false`. |
| `expires_at` | string | No | The invitation expiration time in RFC 3339 format; omitted for no fixed expiration. The format is `date-time`. |
| `member_limit` | integer | No | The maximum number of concurrent members admitted by this invitation, from 1 to 99999. |
| `name` | string | No | The invitation's optional display label. The maximum length is 32 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
