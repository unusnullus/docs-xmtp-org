# UpdateChannelPost

A new post in a public channel.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `channel_post` | [ContentMessage](contentmessage.md) | Yes | The new channel post, attributed to its public channel. |
| `created_at` | string | Yes | This records when the new channel post update was created, in RFC 3339 format. The format is `date-time`. |
| `type` | `"channel_post"` | Yes | The update type is channel_post. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
