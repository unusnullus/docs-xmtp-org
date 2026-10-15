# Reply

The reply target returned with a message, including any quoted excerpt.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_id` | string | No | This is the chat ID, encoded as a decimal string. A private chat ID differs from the user's ID. The value must match `^[1-9][0-9]*$`. |
| `checklist_task_id` | string | No | This is the ID of the checklist task that this reply refers to. The minimum length is 1 character. The maximum length is 64 characters. |
| `external` | object | No | This describes a reply target whose full message is unavailable here. |
| `external.content_type` | string | Yes | This is the content type of the original message. |
| `external.has_media_spoiler` | boolean | Yes | This is true when the original media is hidden behind a spoiler. |
| `external.origin` | [ForwardOrigin](forwardorigin.md) | Yes | This identifies the origin of the original message. |
| `message` | [Message](message.md) | No | This is the message associated with the event or reply. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `poll_option_id` | string | No | This is the ID of the poll option that this reply refers to. The minimum length is 1 character. The maximum length is 64 characters. |
| `quote` | [Quote](quote.md) | No | This is the quoted portion of the message that you are replying to. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
