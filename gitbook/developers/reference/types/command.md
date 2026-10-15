# Command

One command displayed in your bot's command menu.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `command` | string | Yes | This is the command name without the leading slash. The value must match `^[a-z0-9_]{1,32}$`. |
| `description` | string | Yes | Users see this description beside the command in the command menu. You can use up to 256 UTF-16 code units after markup is parsed. |
| `is_ephemeral` | boolean | No | This flag is accepted and stored with the command. It does not change command behavior yet. The default is `false`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
