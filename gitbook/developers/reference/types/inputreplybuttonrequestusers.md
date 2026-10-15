# InputReplyButtonRequestUsers

A reply button that asks the user to select accounts to share with your bot.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `max_quantity` | integer | No | This is the maximum number of users that can be shared through this button. The minimum is 1. The maximum is 10. The default is `1`. |
| `request_id` | string | Yes | Choose an identifier for the request_users button. The users_shared event returns the same value. You can use up to 64 UTF-16 code units after markup is parsed. The value must match `^[A-Za-z0-9_-]{1,64}$`. The minimum length is 1 character. |
| `request_photo` | boolean | No | Set this to true to request profile photos when the user shares accounts. |
| `request_username` | boolean | No | Set this to true to request handles when the user shares accounts. |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `text` | string | Yes | The label displayed on this button. You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `type` | `"request_users"` | Yes | The button action type is request_users. |
| `user_is_bot` | boolean | No | This is true when the shared accounts must be bots. Omit this field to allow either account type. |
| `user_is_premium` | boolean | No | This is true when shared accounts must have premium status. Omit this field to allow either status. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
