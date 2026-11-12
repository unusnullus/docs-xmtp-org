# GroupMessages

The requested messages your bot can currently read, in ascending message_id order. Missing, deleted, expired and unreadable IDs are omitted.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `messages` | Array of [GroupMessage](groupmessage.md) | Yes | Messages in ascending message_id order, in the same shape as group message updates. The maximum is 100 items. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
