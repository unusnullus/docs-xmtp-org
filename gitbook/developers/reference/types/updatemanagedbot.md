# UpdateManagedBot

An update reporting a Managed bot and its human owner.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | When this update was created, in RFC 3339 format. The format is `date-time`. |
| `managed_bot` | [ManagedBot](managedbot.md) | Yes | The Managed bot and its human owner. |
| `type` | `"managed_bot"` | Yes | The update type is managed_bot. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
