# SendBusinessEnvelopes

One encrypted Business operation with its complete set of device copies.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `callback_query_id` | string | No | The received button query ID, required for callback_answer. The maximum length is 256 characters. |
| `content_commitment` | string | Yes | The same 32-byte content commitment, encoded in base64, on every device copy. The minimum length is 44 characters. The maximum length is 44 characters. |
| `dice_emoji` | string | No | Reserved. Business dice is currently unavailable. The maximum length is 16 characters. |
| `envelopes` | Array of object | Yes | The complete set of encrypted device copies for this operation. The minimum is 1 item. The maximum is 150 items. |
| `envelopes[]` | object | Per item | Only the listed fields are allowed. |
| `envelopes[].ciphertext` | string | Yes | The Signal ciphertext addressed to this device, encoded in base64. The minimum length is 1 character. The maximum length is 16,000 characters. |
| `envelopes[].content_commitment` | string | Yes | The same content commitment as the complete operation. The minimum length is 44 characters. The maximum length is 44 characters. |
| `envelopes[].conversation_peer` | string | No | The peer registration ID on an owner-device copy. Omit it on peer-device copies. The maximum length is 256 characters. |
| `envelopes[].to` | string | Yes | The explicit registration ID and installation token of a current peer or owner device. The maximum length is 512 characters. |
| `envelopes[].type` | string | Yes | The encrypted event type allowed for the selected operation. |
| `message_id` | string | No | The original message UUID, required for edit, pin and unpin. The format is `uuid`. |
| `message_ref` | string | Yes | The UUID identifying this operation. Reuse it with the same body and Idempotency-Key. The format is `uuid`. |
| `operation` | `"message"` or `"edit"` or `"delete"` or `"pin"` or `"unpin"` or `"chat_action"` or `"read"` or `"callback_answer"` or `"session_heal"` | Yes | The Business operation represented by every device copy. |
| `target_message_ids` | Array of string | No | The one through 100 message UUIDs in this chat to delete. Required for delete. The maximum is 100 items. Each item must be unique. |
| `target_message_ids[]` | string | Per item | The format is `uuid`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
