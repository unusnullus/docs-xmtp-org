# PaymentTokenAmount

An exact transfer quantity encoded in base units with its configured asset and decimals.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `amount` | string | Yes | The exact amount in asset base units, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. The maximum length is 78 characters. |
| `asset` | string | Yes | The payment asset identifier, including its network and token contract or native asset. |
| `contract` | string or null | No | The configured token contract address, or null for a native transfer. |
| `decimals` | integer | Yes | The number of decimal places configured for this token. The minimum is 0. The maximum is 255. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
