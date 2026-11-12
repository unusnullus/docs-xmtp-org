# InputReplyButtonRequestPoll

A reply button that asks the user to create a poll.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `poll_type` | `"regular"` or `"quiz"` | No | This is the type of poll that the user can share through this button. |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `text` | string | Yes | You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `type` | `"request_poll"` | Yes | The button action type is request_poll. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
