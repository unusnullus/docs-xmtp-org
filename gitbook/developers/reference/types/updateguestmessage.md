# UpdateGuestMessage

A Guest invocation for this bot only. It conveys no permission to read history or join the source chat.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | When this update was created, in RFC 3339 format. The format is `date-time`. |
| `guest_message` | [GuestMessage](guestmessage.md) | Yes | The Guest invocation assigned exclusively to this bot. |
| `type` | `"guest_message"` | Yes | The update type is guest_message. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
