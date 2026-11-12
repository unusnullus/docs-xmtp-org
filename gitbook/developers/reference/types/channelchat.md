# ChannelChat

The identity of a public channel. A compact reference may omit its title.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `id` | string | Yes | The identifier of this object. The value must match `^[1-9][0-9]*$`. |
| `kind` | `"channel"` | Yes | This identity belongs to a public channel. |
| `title` | string | No | This is the title displayed for the object. The minimum length is 1 character. The maximum length is 128 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
