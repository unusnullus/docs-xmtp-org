# BusinessConnection

The connected human account, its permissions, and its connection state. Reading this object does not start a private chat or grant permission to send messages.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `connected_at` | string | Yes | UTC time when the human accepted this connection. The format is `date-time`. |
| `id` | string | Yes | The opaque identifier of this business connection. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |
| `is_enabled` | boolean | Yes | True when this connection is active and its registered endpoint and certificate are current. |
| `rights` | object | Yes | The permissions accepted by the connected human account. Current connection and endpoint checks still apply to each operation. |
| `rights.can_delete_all_messages` | boolean | Yes | Delete messages in the permitted conversation scope. |
| `rights.can_delete_sent_messages` | boolean | Yes | Delete messages previously sent by this connection. |
| `rights.can_edit_bio` | boolean | Yes | Change the connected account biography. |
| `rights.can_edit_profile_photo` | boolean | Yes | Change the connected account profile photo. |
| `rights.can_edit_username` | boolean | Yes | Change the connected account username. |
| `rights.can_read_messages` | boolean | Yes | Read messages covered by the connected account consent. |
| `rights.can_reply` | boolean | Yes | Reply to messages within the permitted reply window. |
| `rights.can_send_contacts` | boolean | Yes | Send contacts within the permitted conversation scope. |
| `status` | `"pending_endpoint"` or `"pending_certificate"` or `"active"` or `"suspended"` or `"revoking"` or `"revoked"` | Yes | The connection lifecycle state. |
| `suspended_reason` | string | No | The reason this connection is suspended, when available. |
| `user` | [User](user.md) | Yes | The human account that accepted this connection. |
| `user_chat_id` | string | Yes | The reserved private namespace for the connected account. Sending requires the human to start the bot separately. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
