# MessageBatch

The messages created by a copy or forward request and any source IDs that were skipped.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `messages` | Array of [Message](message.md) | Yes | These are the messages returned by the operation, in result order. |
| `skipped` | Array of string | Yes | These are the source message IDs that the operation did not process. |
| `skipped[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
