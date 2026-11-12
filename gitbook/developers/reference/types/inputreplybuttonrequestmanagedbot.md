# InputReplyButtonRequestManagedBot

A private manager reply button that opens the human creation sheet. Request IDs must be unique within the keyboard. The server verifies the stored original and issues the short-lived creation reference; this button never grants creation authority.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `request_id` | string | Yes | Choose an identifier for this Managed bot request button. The creation request returns the same value. The value must match `^[A-Za-z0-9_-]{1,64}$`. The minimum length is 1 character. The maximum length is 64 characters. |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `suggested_name` | string | No | You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `suggested_username` | string | No | An optional username suggested for the new bot. The user can edit it before confirming creation. The value must match `^[A-Za-z0-9]+$`. The minimum length is 1 character. The maximum length is 64 characters. |
| `text` | string | Yes | You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `type` | `"request_managed_bot"` | Yes | The button action type is request_managed_bot. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
