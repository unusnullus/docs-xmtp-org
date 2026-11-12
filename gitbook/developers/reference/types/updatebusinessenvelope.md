# UpdateBusinessEnvelope

A raw encrypted Business update for polling or webhook delivery.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `business_envelope` | [BusinessEnvelope](businessenvelope.md) | Yes | The encrypted Business event. |
| `created_at` | string | Yes | When the original event was received, in RFC 3339 format. The format is `date-time`. |
| `type` | `"business_envelope"` | Yes | The update type is business_envelope. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
