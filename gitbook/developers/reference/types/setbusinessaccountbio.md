# SetBusinessAccountBio

The connected account and its new biography. An empty biography clears the existing text.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `bio` | string | Yes | Zero to forty UTF-16 code units; exact text, empty clears. The owning User validator enforces this unit. |
| `business_connection_id` | string | Yes | The connection identifying the human account whose biography is being changed. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
