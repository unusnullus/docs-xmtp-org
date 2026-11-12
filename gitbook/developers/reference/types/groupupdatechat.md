# GroupUpdateChat

Arguments for chats.update in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation.

The response includes at least one of `title` or `description`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `description` | string | No | This is the description displayed for the object. The maximum length is 255 characters. |
| `title` | string | No | This is the title displayed for the object. The minimum length is 1 character. The maximum length is 128 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
