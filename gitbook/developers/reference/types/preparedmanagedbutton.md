# PreparedManagedButton

A saved request for native managed-bot creation. The known human reviews and confirms creation; suggested names remain editable.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `request_id` | string | Yes | Use this identifier to correlate a response or a users_shared event with its request. The value must match `^[A-Za-z0-9_-]{1,64}$`. The minimum length is 1 character. The maximum length is 64 characters. |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `suggested_name` | string | No | An editable bot name suggestion from the original Managed request button. The minimum length is 1 character. The maximum length is 64 characters. |
| `suggested_username` | string | No | An editable username suggestion. It does not reserve a username or authorize creation. The value must match `^[A-Za-z0-9]+$`. The minimum length is 1 character. The maximum length is 64 characters. |
| `text` | string | Yes | The label displayed on this button. The minimum length is 1 character. The maximum length is 64 characters. |
| `type` | `"request_managed_bot"` | Yes | The button action type is request_managed_bot. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
