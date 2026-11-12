# BusinessEnvelopeAcceptance

Confirmation that the complete operation was recorded for delivery. Recipient delivery is confirmed separately.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `accepted` | Array of string | Yes | The device addresses accepted together for this operation. Each item must be unique. |
| `message_ref` | string | Yes | The same UUID supplied for this operation. The format is `uuid`. |
| `rejected` | Array of object | Yes | Empty on success because the complete copy set is accepted together. The maximum is 0 items. |
| `rejected[].reason` | string | Yes | The reason this device copy was rejected. |
| `rejected[].to` | string | Yes | The device address. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
