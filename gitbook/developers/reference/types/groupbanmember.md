# GroupBanMember

Arguments for members.ban in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `revoke_messages` | boolean | No | Revokes access to the group and its server history. In Super Groups either boolean has the effective value true; messages and shared content are retained. The default is `true`. |
| `until_at` | string | No | The membership, restriction or ban expiration time in RFC 3339 format; omitted when indefinite. The format is `date-time`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
