# GroupSenderChat

The real group or channel identity of an anonymous sender or automatic forward. No synthetic user identity is supplied.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `id` | string | Yes | The identifier of this object. The value must match `^[1-9][0-9]*$`. |
| `kind` | `"group"` or `"channel"` | Yes | The chat, message or sender variant. |
| `title` | string | Yes | This is the title displayed for the object. |
| `username` | string | No | The public username of the group or channel, when assigned. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
