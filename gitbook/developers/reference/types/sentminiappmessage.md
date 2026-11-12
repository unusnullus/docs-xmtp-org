# SentMiniAppMessage

The accepted delivery mode and optional reference for an interactive message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `delivery` | `"sent"` or `"client_mediated"` | Yes | Whether the result was sent by the server or accepted for delivery by the launching client. |
| `inline_message_id` | string | No | Use this reference for operations on the interactive message. The value must match `^[0-9a-f]{32}$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
