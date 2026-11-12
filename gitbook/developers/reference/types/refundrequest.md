# RefundRequest

The original refund acceptance receipt, returned with HTTP 202. It is not proof of a completed refund.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `expires_at` | string | Yes | The original request expiry in UTC. Replaying an accepted request preserves this time, including after expiry; current progress arrives through payment updates. The format is `date-time`. |
| `refund_id` | string | Yes | The refund request identifier assigned by the payment service. Preserve it when retrying. The value must match `^[A-Za-z0-9_:-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `state` | `"awaiting_merchant_transfer"` | Yes | The original acceptance state. The request does not send funds or confirm a return transfer. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
