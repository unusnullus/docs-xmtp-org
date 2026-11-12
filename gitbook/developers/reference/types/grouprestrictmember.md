# GroupRestrictMember

Arguments for members.restrict in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `permissions` | [GroupPermissions](grouppermissions.md) | Yes | The group defaults or member permission values. Effective member permissions intersect the applicable group defaults. |
| `until_at` | string | No | The membership, restriction or ban expiration time in RFC 3339 format; omitted when indefinite. The format is `date-time`. |
| `use_independent_chat_permissions` | boolean | No | Whether to preserve independently supplied permission values instead of applying the documented implication rules. The default is `false`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
