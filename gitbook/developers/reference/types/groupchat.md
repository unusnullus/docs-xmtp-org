# GroupChat

A server-managed Super Group visible to a bot. Its decimal chat ID is not a user ID. Group features require both nexo_groups.enabled and nexo_groups.bots; this model does not grant membership or enable an operation.

The response omits `bot`, `user`, `operator_epoch`, and `started`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `id` | string | Yes | The identifier of this object. The value must match `^[1-9][0-9]*$`. |
| `is_forum` | boolean | No | This is true when the chat has forum topics. |
| `kind` | `"group"` | Yes | The chat, message or sender variant. |
| `title` | string | No | This is the title displayed for the object. The minimum length is 1 character. The maximum length is 128 characters. |
| `username` | string | No | The group's public username, when one is assigned. The value must match `^[A-Za-z0-9]{8,24}$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
