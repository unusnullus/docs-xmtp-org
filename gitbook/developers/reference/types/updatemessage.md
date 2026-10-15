# UpdateMessage

An update carrying a new message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | This records when the new message update was created, in RFC 3339 format. The format is `date-time`. |
| `message` | [Message](message.md) | Yes | This is the message associated with the event or reply. |
| `type` | `"message"` | Yes | The update type is message. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
