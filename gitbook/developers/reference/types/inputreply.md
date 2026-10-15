# InputReply

The message to reply to, with an optional quote and a choice of how to handle a missing target.

Only the listed fields are allowed. Do not supply `checklist_task_id` and `poll_option_id` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `allow_missing` | boolean | No | Set this to true to send the message if the reply target is unavailable. |
| `chat_id` | string | No | This is the chat ID, encoded as a decimal string. A private chat ID differs from the user's ID. The value must match `^[1-9][0-9]*$`. |
| `checklist_task_id` | string | No | This is the ID of the checklist task that this reply refers to. The minimum length is 1 character. The maximum length is 64 characters. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `poll_option_id` | string | No | This is the ID of the poll option that this reply refers to. The minimum length is 1 character. The maximum length is 64 characters. |
| `quote` | [InputQuote](inputquote.md) | No | This is the quoted portion of the message that you are replying to. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
