# UpdateStoppedMessageGeneration

An update carrying a user stopping draft generation.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | This records when the stopped generation update was created, in RFC 3339 format. The format is `date-time`. |
| `stopped_message_generation` | [StoppedMessageGeneration](stoppedmessagegeneration.md) | Yes | You receive this event when a user stops your bot's draft generation. |
| `type` | `"stopped_message_generation"` | Yes | The update type is stopped_message_generation. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
