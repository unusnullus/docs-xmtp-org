# BotSubscriptionUpdated

A subscription state change. Each renewal requires a new wallet approval.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `canceled_by` | `"user"` or `"bot"` or `"admin"` | No | The actor who canceled renewal of the subscription. |
| `current_period_end_at` | string | Yes | The end of the currently paid subscription period. The format is `date-time`. |
| `invoice_payload` | string | No | The opaque invoice payload returned only to the merchant bot. |
| `state` | `"active"` or `"canceled"` or `"past_due"` or `"expired"` or `"refunded"` or `"ended"` | Yes | This is the current processing state of the file or link preview. |
| `subscription_id` | string | Yes | The subscription identifier owned by this merchant. |
| `user` | [User](user.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
