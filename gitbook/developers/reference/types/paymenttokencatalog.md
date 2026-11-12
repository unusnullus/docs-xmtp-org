# PaymentTokenCatalog

The configured assets available for bot payments. Invoice admission separately checks the merchant's accepted assets.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `tokens` | Array of [PaymentSupportedToken](paymentsupportedtoken.md) | Yes | Assets returned by the current payment catalog, each with its network and base-unit metadata. The maximum is 4,096 items. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
