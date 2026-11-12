# RefundRequestUpdated

A refund request state. The creator must send the return transfer.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `charge_id` | string | Yes | The identifier of the confirmed payment charge. |
| `refund_id` | string | Yes | The refund request identifier. A pending request does not confirm a transfer. |
| `state` | `"awaiting_merchant_transfer"` or `"submitted"` or `"confirmed"` or `"expired"` | Yes | This is the current processing state of the file or link preview. |
| `user` | [User](user.md) | No | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
