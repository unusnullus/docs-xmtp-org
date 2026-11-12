# PaymentPayoutReadiness

The current payout wallet's setup status on one network. Wallet addresses are not included.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chain` | string | Yes | The configured payout network in CAIP-2 form. The value must match `^eip155:[1-9][0-9]*$`. |
| `registered` | boolean | Yes | Whether the current payout wallet is registered with the payment service for this bot and network. |
| `verified` | boolean | Yes | Whether the payment service has verified the current payout wallet. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
