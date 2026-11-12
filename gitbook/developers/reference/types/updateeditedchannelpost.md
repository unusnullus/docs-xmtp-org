# UpdateEditedChannelPost

An edited post in a public channel.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | This records when the channel post edit update was created, in RFC 3339 format. The format is `date-time`. |
| `edited_channel_post` | [ContentMessage](contentmessage.md) | Yes | The edited channel post, retaining its original send time and channel identity. |
| `type` | `"edited_channel_post"` | Yes | The update type is edited_channel_post. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
