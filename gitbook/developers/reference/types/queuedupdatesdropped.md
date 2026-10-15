# QueuedUpdatesDropped

A notice identifying updates lost from the delivery queue.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `count` | string | Yes | This is the number of updates that were dropped, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `first_at` | string | Yes | This records the date and time of the first dropped event, in RFC 3339 format. The format is `date-time`. |
| `first_update_id` | string | Yes | This is the first dropped update ID, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `last_at` | string | Yes | This records the date and time of the last dropped event, in RFC 3339 format. The format is `date-time`. |
| `last_update_id` | string | Yes | This is the last dropped update ID, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `reason` | `"expired"` or `"backlog_limit"` or `"access_revoked"` | Yes | This explains why queued updates were lost: expired after their retention period, backlog_limit when the queue filled, or access_revoked when your bot lost access. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
