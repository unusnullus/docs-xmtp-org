# BotAccessSettings

The current access settings of a bot you manage.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `added_users` | Array of [User](user.md) | Yes | Explicitly allowed people. The owner is always permitted and is omitted from this list. The maximum is 10 items. |
| `is_access_restricted` | boolean | Yes | Whether only the owner and listed people may use the bot. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
