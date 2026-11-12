# Subscription

A manually renewed subscription and its confirmed payment history.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `access_until` | string or null | Yes | Reported access deadline, or null when no finite deadline is reported. The format is `date-time`. |
| `canceled_by` | `"user"` or `"bot"` or `"admin"` | No | Actor that canceled renewal, when cancellation is reported. |
| `current_period_end_at` | string | Yes | End of the current paid period, before any grace period. The format is `date-time`. |
| `grace_ends_at` | string | No | End of the grace period, when reported. The format is `date-time`. |
| `last_charge` | object | No | Most recent reported charge reference; it does not by itself establish current access. |
| `last_charge.at` | string | Yes | Time of the reported charge in RFC 3339 format. The format is `date-time`. |
| `last_charge.charge_id` | string | Yes | Opaque identifier of the reported charge. The minimum length is 1 character. |
| `last_charge.tx_hash` | string | Yes | Transaction hash recorded for the reported charge. The minimum length is 1 character. |
| `periods_paid` | integer | Yes | Number of confirmed paid periods. The minimum is 0. The maximum is 9,007,199,254,740,991. |
| `plan_id` | string | Yes | Payment plan associated with this subscription. The minimum length is 1 character. |
| `renewal_due_at` | string | No | Time at which the manual renewal becomes due, when reported. The format is `date-time`. |
| `started_at` | string | Yes | Start of the first period still backed by a confirmed charge, recalculated after a reorg. The format is `date-time`. |
| `state` | `"active"` or `"past_due"` or `"canceled"` or `"expired"` or `"refunded"` or `"ended"` | Yes | Current subscription lifecycle state; check entitlement for the access decision. |
| `subscription_id` | string | Yes | Opaque identifier of this subscription. The minimum length is 1 character. |
| `total_paid` | Array of [PaymentTokenAmount](paymenttokenamount.md) | Yes | Gross confirmed charges in base units, excluding reorged charges and without subtracting refunds. |
| `user` | [User](user.md) | Yes | Buyer associated with this subscription. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
