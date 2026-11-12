# GroupExternalReplyVisible

An admitted public group or channel source, with both canonical source IDs. The actual source author remains distinct from the destination actor.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_id` | string | Yes | This is the chat ID, encoded as a decimal string. A private chat ID differs from the user's ID. The value must match `^[1-9][0-9]*$`. |
| `content_type` | string | Yes | This is the content type of the original message. The value must match `^[a-z_]{1,64}$`. |
| `has_media_spoiler` | boolean | Yes | This is true when the original media is hidden behind a spoiler. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `origin` | [GroupExternalOriginVisible](groupexternaloriginvisible.md) | Yes | This identifies the origin of the original message. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
