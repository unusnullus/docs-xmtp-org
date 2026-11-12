# ManagedBotCreatedService

A platform service message confirming the managed bot created by the human in the private chat with its manager.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `bot` | [User](user.md) | Yes | This identifies the bot whose ownership changed. |
| `type` | `"managed_bot_created"` | Yes | The service event type is managed_bot_created. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
