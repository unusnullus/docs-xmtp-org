# CopyMessages

The source message IDs and optional replacements for a copy request.

Only the listed fields are allowed. If `caption_entities` is present, also include `caption`. If `caption_format` is present, also include `caption`. If any of `reply_markup` or `caption` is present, `message_ids` must contain at most 1 item. Do not supply `caption_format` and `caption_entities` together. If `remove_caption` is `true`, omit `caption`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [InputEntity](inputentity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `effect_id` | string | No | Use a message effect identifier returned by message_effects.list. |
| `from_chat_id` | string | Yes | This is the ID of the chat containing the messages that you want to copy or forward. The value must match `^[1-9][0-9]*$`. |
| `message_ids` | Array of string | Yes | Supply source message IDs as decimal strings in strictly increasing order. The minimum is 1 item. The maximum is 100 items. Each item must be unique. |
| `message_ids[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |
| `protect_content` | boolean | No | Set this to true to protect the new message against copying and forwarding. |
| `remove_caption` | boolean | No | Set this to true to remove the source caption from the copied message. |
| `reply_markup` | [ReplyMarkup](replymarkup.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `reply_to` | [InputReply](inputreply.md) | No | This identifies the reply target and optional quote for the message. |
| `silent` | boolean | No | Set this to true to send or pin the message without a notification sound. |
| `video_start_seconds` | number | No | This is the initial video playback position for the copied or forwarded message, in seconds. Set a new video playback start before the end of the video. Other message types are unchanged. The minimum is 0. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
