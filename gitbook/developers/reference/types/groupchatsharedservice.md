# GroupChatSharedService

A group or channel selected through request_chat. Delivered in the private source conversation, with only the title, username and photo requested by its button. Sharing never grants access to the selected chat.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_id` | string | Yes | This is the chat ID, encoded as a decimal string. A private chat ID differs from the user's ID. The value must match `^[1-9][0-9]*$`. |
| `photo` | [Photo](photo.md) | No | This contains the photo sizes or photo source for the object. |
| `request_id` | string | Yes | Use this identifier to correlate a response or a users_shared event with its request. The minimum length is 1 character. The maximum length is 64 characters. |
| `title` | string | No | This is the title displayed for the object. |
| `type` | `"chat_shared"` | Yes | The service or update type selecting this payload. |
| `username` | string | No | The public username of the group or channel, when assigned. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
