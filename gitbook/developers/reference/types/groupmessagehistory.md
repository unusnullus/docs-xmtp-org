# GroupMessageHistory

One page of group history visible to your bot. Deleted, expired and ephemeral messages are omitted; edited messages appear in their latest revision.

The response does not contain `next_before_message_id` and `next_after_message_id` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `has_more` | boolean | Yes | Whether another page may exist in the requested direction. A page can contain fewer than limit messages while has_more is true. |
| `messages` | Array of [GroupMessage](groupmessage.md) | Yes | Messages in ascending message_id order, in the same shape as group message updates. The maximum is 100 items. |
| `next_after_message_id` | string | No | Pass as after_message_id to read the next newer page. Present when has_more is true for an after_message_id read. The value must match `^[1-9][0-9]*$`. |
| `next_before_message_id` | string | No | Pass as before_message_id to read the next older page. Present when has_more is true for a latest or before_message_id read. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
