# SuccessfulPaymentService

A transfer confirmed by the payment service. Deliver the purchase only after this service event.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chain` | string | Yes | The network identifier for this transfer. |
| `charge_id` | string | Yes | The identifier of the confirmed payment charge. |
| `invoice_payload` | string | Yes | The opaque invoice payload returned only to the merchant bot. |
| `token` | [PaymentTokenAmount](paymenttokenamount.md) | Yes | The verified transfer with its exact base-unit amount and configured decimals. |
| `tx_hash` | string | Yes | The transaction hash verified by the payment service. |
| `type` | `"successful_payment"` | Yes | The service event type is successful_payment. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
