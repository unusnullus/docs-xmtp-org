# PaymentRevertedService

A service message reporting that a previously confirmed payment was reverted.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `charge_id` | string | Yes | The charge whose confirmed transfer was reverted. |
| `type` | `"payment_reverted"` | Yes | The service event type is payment_reverted. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
