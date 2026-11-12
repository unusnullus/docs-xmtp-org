# Entitlement

The platform's current access decision for a buyer.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `access_until` | string or null | Yes | Reported access deadline, or null when no finite deadline is reported; use has_access for the verdict. The format is `date-time`. |
| `has_access` | boolean | Yes | Whether the platform currently grants access. |
| `plan_id` | string | No | Payment plan associated with this access, when reported. The minimum length is 1 character. |
| `source` | `"none"` or `"one_time"` or `"subscription"` or `"comp"` | Yes | Source supporting the current access verdict. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
