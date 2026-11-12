# PersonalChannelChat

Metadata for a user's currently visible personal public channel.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `description` | string | No | The current public channel description, when available. |
| `has_protected_content` | boolean | Yes | Whether the public channel restricts saving and forwarding its content. |
| `id` | string | Yes | The canonical public channel ID, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `is_forum` | `false` | Yes | Always false for a personal public channel. |
| `kind` | `"channel"` | Yes | The personal chat kind is channel. |
| `signatures_enabled` | boolean | Yes | Whether the public channel signs its posts. |
| `title` | string | Yes | The current public channel title. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
