# SuccessfulPaymentService

A transfer confirmed by the payment service. Deliver the purchase only after this service event.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chain` | string | Yes | The network identifier for this transfer. |
| `charge_id` | string | Yes | The identifier of the confirmed payment charge. |
| `duplicate_of_charge_id` | string | No | For a duplicate payment, identifies the original canonical charge. Omitted for an original payment. |
| `invoice_payload` | string | Yes | The opaque invoice payload returned only to the merchant bot. |
| `token` | [PaymentTokenAmount](paymenttokenamount.md) | Yes | The actual merchant transfer with its configured decimals. amount is the net value received by the bot in token base units after deducting any platform commission; pre_checkout_query.amount is the full invoice price. |
| `tx_hash` | string | Yes | The transaction hash verified by the payment service. |
| `type` | `"successful_payment"` | Yes | The service event type is successful_payment. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
