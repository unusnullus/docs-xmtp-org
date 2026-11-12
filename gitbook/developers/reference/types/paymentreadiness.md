# PaymentReadiness

Current payment setup checks for this bot. Not-ready is a valid result; a failed read returns an error.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `delivery_mode` | `"none"` or `"polling"` or `"webhook"` or `"internal"` | Yes | The bot's current update delivery mode. None means delivery is not configured. |
| `missing` | Array of string | Yes | Setup items to complete, using the same identifiers as a checkout rejection. Empty when ready is true. The maximum is 70 items. Each item must be unique. |
| `missing[]` | string | Per item | The value must match `^(terms\|support_contact\|merchant_terms\|delivery_mode\|allowed_updates:(pre_checkout_query\|shipping_query)\|payout_address:eip155:[1-9][0-9]*)$`. |
| `payment_updates_allowed` | [PaymentUpdateReadiness](paymentupdatereadiness.md) | Yes | The payment-related update types enabled for this bot. |
| `payout_addresses` | Array of [PaymentPayoutReadiness](paymentpayoutreadiness.md) | Yes | Current payout setup by configured network, with each network appearing once. The maximum is 64 items. |
| `polling_backlog` | integer | No | Pending updates when supplied for polling delivery. More than 1000 pending updates indicates a delivery backlog; absence means the count was not supplied. The minimum is 0. The maximum is 9,007,199,254,740,991. |
| `ready` | boolean | Yes | Whether the current merchant setup passes the payment service's checkout checks. This is not a payment confirmation. |
| `reason` | `"payout_address_missing"` or `"checklist_incomplete"` | No | The checkout rejection reason when ready is false. Omitted when ready is true. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
