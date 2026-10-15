# PollOptionDeletedService

A service event identifying an option removed from a poll.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `option` | [PollOption](polloption.md) | Yes | This is the poll option added or removed by the service event. |
| `poll_message_id` | string | Yes | This is the ID of the message containing the poll. The value must match `^[1-9][0-9]*$`. |
| `type` | `"poll_option_deleted"` | Yes | The service event type is poll_option_deleted. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
