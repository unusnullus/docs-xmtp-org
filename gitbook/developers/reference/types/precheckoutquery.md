# PreCheckoutQuery

The merchant approval query sent before a transfer, with its original ten-second deadline.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `amount` | string | Yes | The exact amount in asset base units, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. The maximum length is 78 characters. |
| `chain` | string | Yes | The network identifier for this transfer. |
| `decimals` | integer | No | The number of decimal places configured for this token. The minimum is 0. The maximum is 255. |
| `expires_at` | string | Yes | The original pre-checkout deadline in RFC 3339 format. Answer this query before this time; retries do not extend it. The format is `date-time`. |
| `id` | string | Yes | The identifier of this object. |
| `invoice_payload` | string | Yes | The opaque invoice payload returned only to the merchant bot. |
| `recipient` | string | Yes | The configured creator payout address for this transfer. |
| `token` | string or null | No | The configured token contract address, or null for a native transfer. |
| `user` | [User](user.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
