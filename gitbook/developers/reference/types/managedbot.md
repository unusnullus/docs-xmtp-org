# ManagedBot

A Managed bot and the human owner represented by this update.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `bot` | [User](user.md) | Yes | The Managed bot's public profile. |
| `is_managed` | boolean | Yes | The bot's Managed status reported by this update. |
| `user` | [User](user.md) | Yes | The bot's human owner. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
