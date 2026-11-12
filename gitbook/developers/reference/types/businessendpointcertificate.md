# BusinessEndpointCertificate

Public Business endpoint data for BusinessEndpointCertificate. No runtime private key is accepted or returned.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `payload` | object | Yes | The exact typed fields signed by this proof or human certificate. |
| `payload.bot_id` | string | Yes | The bound account identifier as a canonical decimal string. The value must match `^[1-9][0-9]{0,19}$`. |
| `payload.bot_username` | string | Yes | The username of the bot authorized by the human certificate. The minimum length is 1 character. The maximum length is 128 characters. |
| `payload.cert_seq` | integer | Yes | The positive human-certificate sequence within the JSON exact-integer range. The minimum is 1. The maximum is 9,007,199,254,740,991. |
| `payload.connection_id` | string | Yes | The opaque canonical Base64url Business connection identifier. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |
| `payload.endpoint_installation_token` | string | Yes | The reserved botc_ installation identifier. It is not a human login credential. The value must match `^botc_`. The minimum length is 6 characters. The maximum length is 256 characters. |
| `payload.fingerprint` | string | Yes | The Signal 60-digit safety number binding this endpoint identity to the connection and owner. The value must match `^[0-9]{60}$`. |
| `payload.identity_key` | string | Yes | The canonical padded Base64 serialized Signal public key, including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `payload.issued_at` | integer | Yes | The certificate issue time in UTC Unix seconds. The minimum is 0. The maximum is 9,007,199,254,740,991. |
| `payload.owner_registration_id` | string | Yes | The bound account identifier as a canonical decimal string. The value must match `^[1-9][0-9]{0,19}$`. |
| `payload.rights` | object | Yes | The human-approved rights visible to peers. A certificate alone grants no operation. |
| `payload.rights.can_delete_all_messages` | boolean | Yes | Whether the human permits deletion of other messages in scope. |
| `payload.rights.can_delete_sent_messages` | boolean | Yes | Whether the human permits deletion of the bot's sent messages. |
| `payload.rights.can_read_messages` | boolean | Yes | Whether the human permits message access in scope. |
| `payload.rights.can_reply` | boolean | Yes | Whether the human permits Business replies. |
| `payload.rights.can_send_contacts` | boolean | Yes | Whether the human permits sharing contact cards. |
| `payload.v` | `1` | Yes | Certificate format version 1. |
| `signature` | string | Yes | The canonical padded Base64 XEdDSA signature. The minimum length is 88 characters. The maximum length is 88 characters. |
| `signer_identity_key` | string | Yes | The canonical padded Base64 serialized Signal public key, including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `signer_installation_token` | string | Yes | The human installation that approved this endpoint certificate. The minimum length is 1 character. The maximum length is 256 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
