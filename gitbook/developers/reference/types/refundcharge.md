# RefundCharge

A refund instruction for a confirmed charge. Recipient, network and asset come from the original charge.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `amount` | string | No | Exact positive base units to refund, no greater than uint256 maximum or the remaining refundable amount. Omit for the full remaining amount; do not send null. The value must match `^[1-9][0-9]*$`. The maximum length is 78 characters. |
| `user_id` | string | Yes | The decimal public ID, available to this bot, of the original payer of the charge. The value must match `^[1-9][0-9]*$`. The maximum length is 20 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
