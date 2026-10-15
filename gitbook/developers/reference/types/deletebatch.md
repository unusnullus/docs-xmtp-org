# DeleteBatch

The IDs deleted by a batch request and the IDs that were skipped.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `deleted` | Array of string | Yes | These are the IDs of the messages deleted by this request. |
| `deleted[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |
| `skipped` | Array of string | Yes | These are the source message IDs that the operation did not process. |
| `skipped[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
