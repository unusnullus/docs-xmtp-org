# DeletedMessage

The identity of a message reported as deleted.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `deleted_at` | string | Yes | This records when the message was deleted, in RFC 3339 format. The format is `date-time`. |
| `kind` | `"deleted"` | Yes | The value deleted identifies a removed message whose content is no longer returned. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `revision` | string | Yes | This is the deleted message's revision, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `state_version` | string | No | This version orders changes to the message's state and is encoded as a decimal string. The value must match `^[0-9]+$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
