# GroupLifecycleService

A group lifecycle service. Additional service-specific fields are retained. Creation, bot-owner notices and payment services are never group bot updates.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `reason` | `"banned"` or `"kicked"` | No | Only on member_left. banned or kicked when an administrator or bot removed the member; the message sender is the moderator. Omitted when the member left voluntarily. |
| `type` | `"members_joined"` or `"member_left"` or `"title_changed"` or `"photo_changed"` or `"photo_deleted"` or `"auto_delete_timer_changed"` or `"owner_left"` or `"owner_changed"` or `"message_pinned"` | Yes | The service or update type selecting this payload. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
