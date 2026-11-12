# GroupGetMessages

The message IDs to read, for example the message_id of a reply reference.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `message_ids` | Array of string | Yes | One to 100 distinct message IDs in this group, encoded as decimal strings. The minimum is 1 item. The maximum is 100 items. Each item must be unique. |
| `message_ids[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
