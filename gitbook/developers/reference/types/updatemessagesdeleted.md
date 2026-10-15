# UpdateMessagesDeleted

An update carrying deleted messages.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | This records when the message deletion update was created, in RFC 3339 format. The format is `date-time`. |
| `messages_deleted` | [MessagesDeleted](messagesdeleted.md) | Yes | These identify the messages that were deleted in the chat. |
| `type` | `"messages_deleted"` | Yes | The update type is messages_deleted. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
