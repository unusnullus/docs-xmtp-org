# PatchProfile

Change the default profile text or one localization. Omitted fields keep their values. Null or an empty string clears an optional field. You cannot clear the default name, but you can clear a localized name.

Only the listed fields are allowed. If `language_code` is omitted or is `""`, `name` must have the type string and must match `\S`. Include at least one of `name`, `description`, or `short_description`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `description` | string or null | No | This is the profile description for your bot. You can use up to 512 UTF-16 code units after markup is parsed. |
| `language_code` | string | No | This is the language tag for this profile or command set. An empty string selects the default. The maximum length is 32 characters. |
| `name` | string or null | No | This is the display name of the user, bot, contact, or provider. You can use up to 64 UTF-16 code units after markup is parsed. |
| `short_description` | string or null | No | This is the short profile description displayed for your bot. You can use up to 120 UTF-16 code units after markup is parsed. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
