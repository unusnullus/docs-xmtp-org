# ProfileReceipt

The resulting profile text and version after an update.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `config_version` | string | Yes | This is the version of your bot's configuration, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `description` | string or null | Yes | This is the profile description for your bot. You can use up to 512 UTF-16 code units after markup is parsed. |
| `language_code` | string | Yes | This is the language tag for this profile or command set. An empty string selects the default. The maximum length is 32 characters. |
| `name` | string or null | Yes | This is the display name of the user, bot, contact, or provider. You can use up to 64 UTF-16 code units after markup is parsed. |
| `profile_version` | string | Yes | This is the version of your bot's profile, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `short_description` | string or null | Yes | This is the short profile description displayed for your bot. You can use up to 120 UTF-16 code units after markup is parsed. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
