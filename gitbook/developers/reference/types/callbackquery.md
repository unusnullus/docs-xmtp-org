# CallbackQuery

An inline button press received by your bot. Answer it with callback_queries.answer before it expires.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_instance` | string | Yes | This is an opaque identifier of the chat associated with the callback. The format is `uuid`. |
| `created_at` | string | Yes | This records when the callback query was created, in RFC 3339 format. The format is `date-time`. |
| `data` | string | Yes | Your bot receives this callback data when the user presses the button. You can use up to 64 UTF-8 bytes. The minimum length is 1 character. |
| `id` | string | Yes | This UUID identifies the callback query. The format is `uuid`. |
| `message` | [MaybeInaccessibleMessage](maybeinaccessiblemessage.md) | Yes | This is the message associated with the event or reply. |
| `user` | [User](user.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
