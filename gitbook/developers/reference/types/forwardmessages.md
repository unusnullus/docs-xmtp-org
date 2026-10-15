# ForwardMessages

The source message IDs and delivery options for a forward request.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `effect_id` | string | No | Use a message effect identifier returned by message_effects.list. |
| `from_chat_id` | string | Yes | This is the ID of the chat containing the messages that you want to copy or forward. The value must match `^[1-9][0-9]*$`. |
| `message_ids` | Array of string | Yes | Supply source message IDs as decimal strings in strictly increasing order. The minimum is 1 item. The maximum is 100 items. Each item must be unique. |
| `message_ids[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |
| `protect_content` | boolean | No | Set this to true to protect the new message against copying and forwarding. |
| `silent` | boolean | No | Set this to true to send or pin the message without a notification sound. |
| `video_start_seconds` | number | No | This is the initial video playback position for the copied or forwarded message, in seconds. Set a new video playback start before the end of the video. Other message types are unchanged. The minimum is 0. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
