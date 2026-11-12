# InvoiceContent

The merchant invoice displayed in a private bot chat.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `amount` | string | Yes | The exact amount in asset base units, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. The maximum length is 78 characters. |
| `asset` | string | Yes | The payment asset identifier, including its network and token contract or native asset. |
| `chain` | string | Yes | The network identifier for this transfer. |
| `decimals` | integer | Yes | The number of decimal places configured for this token. The minimum is 0. The maximum is 255. |
| `description` | string | Yes | This is the description displayed for the object. |
| `invoice_id` | string | Yes | The invoice identifier issued by the payment service. |
| `recipient` | string | Yes | The configured creator payout address for this transfer. |
| `title` | string | Yes | This is the title displayed for the object. |
| `token` | string or null | Yes | The configured token contract address, or null for a native transfer. The minimum length is 1 character. |
| `type` | `"invoice"` | Yes | The content type is invoice. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
