# UpdateEditedMessage

An update carrying an edited message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | This records when the message edit update was created, in RFC 3339 format. The format is `date-time`. |
| `edited_message` | [Message](message.md) | Yes | You receive this message after an edit that your bot can observe. |
| `type` | `"edited_message"` | Yes | The update type is edited_message. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
