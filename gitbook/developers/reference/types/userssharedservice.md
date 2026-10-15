# UsersSharedService

A service event containing users shared through a request_users button.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `request_id` | string | Yes | The identifier from the request_users button that produced this event. The minimum length is 1 character. The maximum length is 64 characters. |
| `type` | `"users_shared"` | Yes | The service event type is users_shared. |
| `users` | Array of [SharedUser](shareduser.md) | Yes | These are the users shared with your bot through a request_users button. The minimum is 1 item. The maximum is 10 items. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
