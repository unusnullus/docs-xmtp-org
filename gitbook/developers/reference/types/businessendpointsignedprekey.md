# BusinessEndpointSignedPrekey

Public Business endpoint data for BusinessEndpointSignedPrekey. No runtime private key is accepted or returned.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `id` | integer | Yes | The nonnegative 32-bit Signal prekey identifier. The minimum is 0. The maximum is 2,147,483,647. |
| `key` | string | Yes | The canonical padded Base64 serialized Signal public key, including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `signature` | string | Yes | The canonical padded Base64 XEdDSA signature. The minimum length is 88 characters. The maximum length is 88 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
