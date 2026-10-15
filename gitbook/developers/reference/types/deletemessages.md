# DeleteMessages

The message IDs to remove from one chat.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `message_ids` | Array of string | Yes | These are message IDs within the specified chat, encoded as decimal strings. The minimum is 1 item. The maximum is 100 items. Each item must be unique. |
| `message_ids[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
