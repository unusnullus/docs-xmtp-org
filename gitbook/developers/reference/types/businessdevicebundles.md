# BusinessDeviceBundles

Authorized human device bundles. Replaying the same GET original returns the exact bundle receipt without consuming another OTP; native_address is independently revalidated canonical identity metadata. A changed device set or authorization requires a new key.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `devices` | Array of object | Yes | Eligible human devices in a stable order. Bot endpoints are excluded before selection. The minimum is 1 item. The maximum is 100 items. |
| `devices[].endpoint_kind` | `"human_device"` | Yes | The persisted human device class. bot_connection never appears in this response. |
| `devices[].identity_key` | string | Yes | Canonical padded Base64 serialized Signal public key including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `devices[].installation_token` | string | Yes | The human device installation used in its Signal session address. The minimum length is 1 character. The maximum length is 255 characters. |
| `devices[].prekey` | object or null | Yes | A one-time prekey, or null for signed-prekey-only fallback after inventory or quota exhaustion. |
| `devices[].signed_prekey` | object | Yes | Public signed prekey, also served when the daily OTP budget is exhausted. |
| `devices[].signed_prekey.id` | integer | Yes | The nonnegative Signal signed-prekey ID. The minimum is 0. The maximum is 2,147,483,647. |
| `devices[].signed_prekey.key` | string | Yes | Canonical padded Base64 serialized Signal public key including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `devices[].signed_prekey.signature` | string | Yes | The canonical Base64 signature by this device's identity key. The minimum length is 88 characters. The maximum length is 88 characters. |
| `native_address` | string | Yes | Canonical Native public_address for this public user, read and revalidated from the server registry. Match decrypted author_contact_id exactly; never infer this value from ciphertext or registration_id. The minimum length is 1 character. The maximum length is 128 characters. |
| `registration_id` | string | Yes | The target human account's canonical decimal registration ID. The value must match `^[1-9][0-9]{0,19}$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
