# StoppedMessageGeneration

The draft generation stopped by the user, identified by chat and draft ID.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat` | [Chat](chat.md) | Yes | This identifies the chat associated with the message or event. |
| `draft_id` | string | Yes | Choose an identifier for this draft. Reuse it when updating the same draft. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `topic_id` | string | No | This is the topic ID within the chat, when the event belongs to a topic. The value must match `^[1-9][0-9]*$`. |
| `user` | [User](user.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
