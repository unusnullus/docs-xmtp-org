# PreparedKeyboardButton

A reusable prepared-button identifier and its absolute expiry, 24 hours after saving.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `expires_at` | string | Yes | The expiration timestamp in RFC 3339 format; null is accepted only where this field is nullable. The format is `date-time`. |
| `id` | string | Yes | The identifier of this object. The value must match `^[a-f0-9]{32}$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
