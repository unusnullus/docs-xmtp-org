# UpdateUpdatesDropped

An update carrying lost updates.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | This records when the lost updates update was created, in RFC 3339 format. The format is `date-time`. |
| `type` | `"updates_dropped"` | Yes | The update type is updates_dropped. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |
| `updates_dropped` | [UpdatesDropped](updatesdropped.md) | Yes | You receive this notice when updates were lost, regardless of your allowed_updates filter. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
