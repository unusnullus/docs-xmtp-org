# PreparedUsersButton

A saved native user-selection request. Only fields explicitly requested by the button are shared. A reusable native user-selection request. Chat selection requires a later platform phase. Managed creation has a separate native confirmation.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `max_quantity` | integer | No | This is the maximum number of users that can be shared through this button. The minimum is 1. The maximum is 10. The default is `1`. |
| `request_id` | string | Yes | Use this identifier to correlate a response or a users_shared event with its request. The minimum length is 1 character. The maximum length is 64 characters. |
| `request_photo` | boolean | No | Set this to true to request profile photos when the user shares accounts. |
| `request_username` | boolean | No | Set this to true to request handles when the user shares accounts. |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `text` | string | Yes | The label displayed on this button. The minimum length is 1 character. The maximum length is 64 characters. |
| `type` | `"request_users"` | Yes | Offer native account selection with the saved filters. |
| `user_is_bot` | boolean | No | This is true when the shared accounts must be bots. Omit this field to allow either account type. |
| `user_is_premium` | boolean | No | This is true when shared accounts must have premium status. Omit this field to allow either status. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
