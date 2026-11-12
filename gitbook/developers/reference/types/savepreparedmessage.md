# SavePreparedMessage

A reusable result for one known human recipient and the allowed destination types.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `allow_bot_chats` | boolean | No | Allow the recipient to share the prepared result to private chats with bots. The default is `false`. |
| `allow_channel_chats` | boolean | No | Allow the recipient to share the prepared result to channels where they may publish. The default is `false`. |
| `allow_group_chats` | boolean | No | Allow the recipient to share the prepared result to groups. The default is `false`. |
| `allow_user_chats` | boolean | No | Allow the recipient to share the prepared result to private chats with people. The default is `false`. |
| `result` | [InlineResult](inlineresult.md) | Yes | The inline result to save for later use as a prepared message. |
| `user_id` | string | Yes | This is the user ID, encoded as a decimal string. A user ID differs from a private chat ID. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
