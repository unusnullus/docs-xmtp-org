# SourceUpdatesDropped

A notice identifying a gap in the source event sequence for a chat.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_id` | string | Yes | This is the chat ID, encoded as a decimal string. A private chat ID differs from the user's ID. The value must match `^[1-9][0-9]*$`. |
| `count` | string | Yes | This is the number of updates that were dropped, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `first_at` | string | No | This records the date and time of the first dropped event, in RFC 3339 format. The format is `date-time`. |
| `first_seq` | string | Yes | This is the first unavailable position in the event sequence, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `last_at` | string | No | This records the date and time of the last dropped event, in RFC 3339 format. The format is `date-time`. |
| `last_seq` | string | Yes | This is the last unavailable position in the event sequence, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `ordering_key` | string | Yes | This is the opaque identifier of the event sequence affected by a source gap. |
| `reason` | `"source_gap"` | Yes | The value source_gap reports missing events for the chat. first_seq and last_seq identify the affected range. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
