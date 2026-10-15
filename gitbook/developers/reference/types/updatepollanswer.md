# UpdatePollAnswer

An update carrying a user's poll answer.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | This records when the poll answer update was created, in RFC 3339 format. The format is `date-time`. |
| `poll_answer` | [PollAnswer](pollanswer.md) | Yes | You receive this vote or vote change from a user. |
| `type` | `"poll_answer"` | Yes | The update type is poll_answer. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
