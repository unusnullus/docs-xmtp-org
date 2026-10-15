# Commands

The command set stored for the requested scope and language.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `bot_ui_version` | string | No | This is the version of your bot's command and menu configuration, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `chat_id` | string | No | This is the chat ID, encoded as a decimal string. A private chat ID differs from the user's ID. |
| `commands` | Array of [Command](command.md) | Yes | These are the commands in the order shown to users. The maximum is 100 items. |
| `config_version` | string | No | This is the version of your bot's configuration, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `language_code` | string | Yes | This is the language tag for this profile or command set. An empty string selects the default. |
| `scope` | `"default"` or `"all_private_chats"` or `"all_group_chats"` or `"all_chat_administrators"` or `"chat"` or `"chat_administrators"` or `"chat_member"` | Yes | This is the scope of the command set, or scope affected by a rate limit error. |
| `user_id` | string | No | This is the user ID, encoded as a decimal string. A user ID differs from a private chat ID. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
