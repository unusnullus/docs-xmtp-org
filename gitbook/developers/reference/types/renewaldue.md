# RenewalDue

An ordered reminder emitted before the paid period ends. Renewal requires a new wallet approval.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `current_period_end_at` | string | Yes | End of the currently paid period. The format is `date-time`. |
| `grace_ends_at` | string or null | Yes | The access deadline, or null when the payment authority reports none. The format is `date-time`. |
| `plan_id` | string | Yes | The payment plan associated with the subscription. |
| `subscription_id` | string | Yes | The subscription requiring a new wallet approval. |
| `user` | [User](user.md) | Yes | The buyer whose subscription is due for manual renewal. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
