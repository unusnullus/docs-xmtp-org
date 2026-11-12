# InputInvoice

A private-chat invoice request using a configured asset and an exact base-unit amount.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `amount` | string | Yes | The exact amount in asset base units, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. The maximum length is 78 characters. |
| `asset` | string | Yes | The payment asset identifier, including its network and token contract or native asset. The minimum length is 1 character. The maximum length is 256 characters. |
| `description` | string | Yes | This is the description displayed for the object. The minimum length is 1 character. The maximum length is 255 characters. |
| `payload` | string | Yes | Send the JSON operation body in the payload part of a multipart request. The minimum length is 1 character. The maximum length is 128 characters. The limit is 128 UTF-8 bytes. |
| `plan_id` | string | No | The configured payment plan linked to this invoice, when present. The minimum length is 1 character. The maximum length is 128 characters. |
| `title` | string | Yes | This is the title displayed for the object. The minimum length is 1 character. The maximum length is 32 characters. |
| `type` | `"invoice"` | Yes | The content type is invoice. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
