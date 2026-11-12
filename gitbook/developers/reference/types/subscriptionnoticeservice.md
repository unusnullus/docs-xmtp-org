# SubscriptionNoticeService

A buyer-visible service message about a manually renewed subscription. This message does not itself create a bot update.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `access_until` | string | No | End of the buyer's access, when reported. The format is `date-time`. |
| `current_period_end_at` | string | No | End of the paid period, when reported. The format is `date-time`. |
| `notice` | `"renewal_due"` or `"past_due"` or `"expired"` or `"canceled"` | Yes | The subscription lifecycle notice shown to the buyer. |
| `reference` | string | Yes | The subscription reference, formed as subscription: followed by subscription_id. |
| `subscription_id` | string | Yes | The subscription associated with this notice. |
| `type` | `"subscription_notice"` | Yes | The service event type is subscription_notice. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
