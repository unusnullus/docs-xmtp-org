# ResolvedCommands

The selected command set and the scope and language that matched.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `commands` | Array of [Command](command.md) | Yes | These are the commands in the order shown to users. The maximum is 100 items. |
| `resolved_language` | string | Yes | This is the language of the command set selected for this user and chat. |
| `resolved_scope` | `"chat"` or `"all_private_chats"` or `"default"` | Yes | This is the scope of the command set selected for this user and chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
