# InputForceReply

A request to open the reply input for the user.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `placeholder` | string | No | This is the placeholder displayed in the message input field. You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `selective` | boolean | No | This is true when the keyboard or reply prompt targets only the relevant users. |
| `type` | `"force_reply"` | Yes | The keyboard type is force_reply. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
