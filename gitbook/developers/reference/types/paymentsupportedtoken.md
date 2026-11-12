# PaymentSupportedToken

A configured asset and its exact payment limits. Native and ERC-20 identities remain distinct.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `asset` | string | Yes | The configured CAIP-19 asset identifier. Use this identity rather than the display symbol. The maximum length is 128 characters. |
| `chain` | string | Yes | The configured EVM network in CAIP-2 form. The value must match `^eip155:[1-9][0-9]*$`. |
| `contract` | string or null | Yes | The canonical ERC-20 token address, or null for the network's native asset. The value must match `^0x[0-9a-f]{40}$`. The minimum length is 42 characters. The maximum length is 42 characters. |
| `decimals` | integer | Yes | Base-unit decimals from the configured wallet catalog. The minimum is 0. The maximum is 77. |
| `max_amount` | string | Yes | The inclusive configured maximum in exact base units, at least min_amount and no greater than uint256 maximum. The value must match `^[1-9][0-9]*$`. The maximum length is 78 characters. |
| `min_amount` | string | Yes | The inclusive configured minimum in exact base units, no greater than uint256 maximum. The value must match `^[1-9][0-9]*$`. The maximum length is 78 characters. |
| `symbol` | string | Yes | The catalog display symbol. It does not identify a token contract. The value must match `^[!-~]+$`. The minimum length is 1 character. The maximum length is 32 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
