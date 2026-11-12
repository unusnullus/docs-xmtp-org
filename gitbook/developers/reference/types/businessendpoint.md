# BusinessEndpoint

Public Business endpoint data for BusinessEndpoint. No runtime private key is accepted or returned.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `cert_seq` | string | Yes | The accepted human-certificate sequence encoded as a decimal string. The value must match `^[0-9]+$`. |
| `certificate` | [BusinessEndpointCertificate](businessendpointcertificate.md) or null | Yes | The current human-signed certificate, or null before human approval. |
| `endpoint_kind` | `"bot_connection"` | Yes | The reserved bot_connection device class, excluded from human device operations. |
| `endpoint_pop` | object | Yes | The typed runtime proof of possession, with its bound payload and XEdDSA signature. |
| `endpoint_pop.payload` | object | Yes | The exact typed fields signed by this proof or human certificate. |
| `endpoint_pop.payload.bot_id` | string | Yes | The bound account identifier as a canonical decimal string. The value must match `^[1-9][0-9]{0,19}$`. |
| `endpoint_pop.payload.connection_id` | string | Yes | The opaque canonical Base64url Business connection identifier. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |
| `endpoint_pop.payload.identity_key` | string | Yes | The canonical padded Base64 serialized Signal public key, including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `endpoint_pop.payload.owner_registration_id` | string | Yes | The bound account identifier as a canonical decimal string. The value must match `^[1-9][0-9]{0,19}$`. |
| `endpoint_pop.payload.signed_prekey_id` | integer | Yes | The nonnegative 32-bit Signal prekey identifier. The minimum is 0. The maximum is 2,147,483,647. |
| `endpoint_pop.signature` | string | Yes | The canonical padded Base64 XEdDSA signature. The minimum length is 88 characters. The maximum length is 88 characters. |
| `fingerprint` | string or null | Yes | The Signal 60-digit safety number binding this endpoint identity to the connection and owner. |
| `identity_key` | string | Yes | The canonical padded Base64 serialized Signal public key, including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `installation_token` | string | Yes | The reserved botc_ installation identifier. It is not a human login credential. The value must match `^botc_[a-f0-9]{32}$`. |
| `prekeys_remaining` | integer | Yes | The number of available one-time prekeys; reading this value consumes none. The minimum is 0. |
| `status` | `"pending_certificate"` or `"active"` or `"suspended"` | Yes | The current connection state; pending and suspended states grant no sending permission. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
