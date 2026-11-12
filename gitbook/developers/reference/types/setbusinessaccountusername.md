# SetBusinessAccountUsername

The connected account and its required new username. Supply the same body and Idempotency-Key when retrying the original request.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `business_connection_id` | string | Yes | The connection identifying the human account whose username is being changed. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `username` | string | Yes | The required new account username, containing 8 to 24 uppercase letters or digits. An empty value does not remove the username. The value must match `^[A-Z0-9]+$`. The minimum length is 8 characters. The maximum length is 24 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
