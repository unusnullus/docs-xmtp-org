# SharedUser

A user shared with your bot through a request_users button.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `photo` | [Photo](photo.md) | No | This contains the photo sizes or photo source for the object. |
| `user_id` | string | Yes | This is the user ID, encoded as a decimal string. A user ID differs from a private chat ID. The value must match `^[1-9][0-9]*$`. |
| `username` | string | No | This is the handle of the user or bot. The value may be absent or null when no handle is available. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
