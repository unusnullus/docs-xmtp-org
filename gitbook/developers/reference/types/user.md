# User

The user identity returned with messages and events, including the ARX app language when known.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `has_photo` | boolean | No | This is true when this user currently has a profile photo. |
| `id` | string | Yes | This is the user ID of the account, encoded as a decimal string. |
| `is_bot` | boolean | Yes | This is true when this account is a bot. |
| `language_code` | string | No | The user's ARX app language, when known. ARX records it when the user interacts with your bot and reuses it for later updates. This field is omitted when no language is known. |
| `name` | string | Yes | This is the display name of the user, bot, contact, or provider. |
| `username` | string or null | No | This is the handle of the user or bot. The value may be absent or null when no handle is available. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
