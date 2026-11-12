# BusinessEnvelope

An encrypted Business event. The SDK decrypts, verifies and projects messages, edits and deletions for typed consumers.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `business_connection_id` | string | Yes | The Business connection receiving this envelope. The minimum length is 1 character. The maximum length is 119 characters. |
| `chat` | object | Yes | The Business chat identified by the chat directory. |
| `chat.id` | string | Yes | The Business chat identifier. The value must match `^[1-9][0-9]*$`. |
| `chat.kind` | `"e2e_business"` | Yes | The chat kind. |
| `ciphertext` | string | Yes | The original encrypted event payload, without decryption or transformation. The minimum length is 1 character. The maximum length is 16,000 characters. |
| `content_commitment` | string | No | The content commitment when the event was emitted by a bot endpoint. |
| `direction` | `"inbound"` or `"owner_sync"` | Yes | Whether the envelope comes from the peer or synchronizes the owner's outgoing activity. |
| `envelope_id` | string | Yes | The original encrypted event identifier. Repeated delivery preserves this identifier. The format is `uuid`. |
| `event_class` | `"message"` or `"delete"` or `"pin"` or `"unpin"` or `"poll_vote"` or `"callback"` or `"session_heal"` | Yes | The visible event class. Message edits are distinguished by the SDK after decryption and verification. |
| `received_at` | string | Yes | When ARX received the original event, in RFC 3339 format. Retries preserve this time. The format is `date-time`. |
| `sender_installation_token` | string | Yes | The sender installation identified by ARX. The minimum length is 1 character. The maximum length is 128 characters. |
| `sender_user` | [User](user.md) | Yes | The sender identified by ARX. |
| `type` | string | Yes | The original Signal ciphertext type. The minimum length is 1 character. The maximum length is 64 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
