# UpdateRenewalDue

An ordered reminder that a subscription needs manual renewal.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | The UTC time reported for this reminder. The format is `date-time`. |
| `pay_seq` | string | No | The payment sequence used to order this update. The value must match `^[1-9][0-9]*$`. |
| `prev_pay_seq` | string | No | The preceding payment sequence. The value must match `^[0-9]+$`. |
| `renewal_due` | [RenewalDue](renewaldue.md) | Yes | The buyer, subscription and deadlines for manual renewal. |
| `type` | `"renewal_due"` | Yes | The update kind, renewal_due. |
| `update_id` | string | Yes | The decimal update identifier used for polling acknowledgement. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
