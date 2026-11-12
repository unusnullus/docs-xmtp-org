# PaymentUpdateReadiness

Payment update types enabled for the bot's current delivery configuration.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `pre_checkout_query` | boolean | Yes | Whether pre-checkout queries are enabled for delivery to this bot. Required to accept payments. |
| `purchased_paid_media` | boolean | Yes | Whether paid-media purchase updates are enabled. |
| `shipping_query` | boolean | Yes | Whether shipping queries are enabled. Required for invoices that request shipping. |
| `subscription_updated` | boolean | Yes | Whether subscription change updates are enabled. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
