# UpdateSubscriptionUpdated

A durable subscription update ordered with this payer's payment events.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | This records when the subscription change update was created, in RFC 3339 format. The format is `date-time`. |
| `pay_seq` | string | No | The durable payment sequence for this bot and payer. The value must match `^[1-9][0-9]*$`. |
| `prev_pay_seq` | string | No | The preceding durable payment sequence for this bot and payer. The value must match `^[0-9]+$`. |
| `subscription_updated` | [BotSubscriptionUpdated](botsubscriptionupdated.md) | Yes | The current subscription state after a manual payment or lifecycle change. |
| `type` | `"subscription_updated"` | Yes | The update type is subscription_updated. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
