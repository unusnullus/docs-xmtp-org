# RegisterBusinessEndpoint

Public Business endpoint data for RegisterBusinessEndpoint. No runtime private key is accepted or returned.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `endpoint_pop` | object | Yes | The typed runtime proof of possession, with its bound payload and XEdDSA signature. Only the listed fields are allowed. |
| `endpoint_pop.payload` | object | Yes | The exact typed fields signed by this proof or human certificate. Only the listed fields are allowed. |
| `endpoint_pop.payload.bot_id` | string | Yes | The bound account identifier as a canonical decimal string. The value must match `^[1-9][0-9]{0,19}$`. |
| `endpoint_pop.payload.connection_id` | string | Yes | The opaque canonical Base64url Business connection identifier. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |
| `endpoint_pop.payload.identity_key` | string | Yes | The canonical padded Base64 serialized Signal public key, including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `endpoint_pop.payload.owner_registration_id` | string | Yes | The bound account identifier as a canonical decimal string. The value must match `^[1-9][0-9]{0,19}$`. |
| `endpoint_pop.payload.signed_prekey_id` | integer | Yes | The nonnegative 32-bit Signal prekey identifier. The minimum is 0. The maximum is 2,147,483,647. |
| `endpoint_pop.signature` | string | Yes | The canonical padded Base64 XEdDSA signature. The minimum length is 88 characters. The maximum length is 88 characters. |
| `identity_key` | string | Yes | The canonical padded Base64 serialized Signal public key, including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `prekeys` | Array of [BusinessEndpointPrekey](businessendpointprekey.md) | Yes | Public one-time prekeys to add. Consumed IDs cannot be reused for this endpoint identity. The maximum is 100 items. |
| `signed_prekey` | [BusinessEndpointSignedPrekey](businessendpointsignedprekey.md) | Yes | The public signed prekey and its identity-key signature. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
