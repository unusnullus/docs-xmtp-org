# GroupCallbackQuery

A callback from a current group member, resolved from the canonical message/button. Privacy does not filter it. The client wait is at most 15 seconds; a bot may answer for 15 minutes. The callback does not grant management rights.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_instance` | string | Yes | This is an opaque identifier of the chat associated with the callback. The minimum length is 1 character. |
| `created_at` | string | Yes | The creation time of this event in RFC 3339 format. The format is `date-time`. |
| `data` | string | Yes | Your bot receives this callback data when the user presses the button. You can use up to 64 UTF-8 bytes. The minimum length is 1 character. |
| `id` | string | Yes | The identifier of this object. The format is `uuid`. |
| `message` | [GroupMessage](groupmessage.md) or [GroupInaccessibleMessage](groupinaccessiblemessage.md) | Yes | This is the message associated with the event or reply. |
| `user` | [User](user.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
