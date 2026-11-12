# MerchantAssetBalance

An on-chain balance in one asset held at the merchant's payout wallet.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `amount` | string | Yes | The observed chain balance in exact base units, from zero to uint256 maximum. The value must match `^(0\|[1-9][0-9]*)$`. The maximum length is 78 characters. |
| `asset` | string | Yes | The configured asset identifier on this network. The value must match `^[!-~]+$`. The minimum length is 1 character. The maximum length is 256 characters. |
| `chain` | string | Yes | The configured EVM network for this balance. The value must match `^eip155:[1-9][0-9]*$`. |
| `decimals` | integer | Yes | Base-unit decimals from the configured asset catalog. The minimum is 0. The maximum is 255. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
