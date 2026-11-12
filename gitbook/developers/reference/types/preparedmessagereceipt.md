# PreparedMessageReceipt

The prepared message identifier and its fixed expiry time.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `expires_at` | string | Yes | The expiration timestamp in RFC 3339 format; null is accepted only where this field is nullable. The format is `date-time`. |
| `id` | string | Yes | The identifier of this object. The value must match `^[0-9a-f]{32}$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
