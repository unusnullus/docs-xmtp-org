# GroupUpdateChatJoinRequest

GroupUpdateChatJoinRequest is a group contract model. Availability is controlled by the evaluated group feature flags.

If `chat_join_request` has the type any JSON value, the response includes `expires_at`. Otherwise, the response omits `expires_at`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_join_request` | [GroupJoinRequest](groupjoinrequest.md) | Yes | The pending group admission request. |
| `created_at` | string | Yes | The creation time of this event in RFC 3339 format. The format is `date-time`. |
| `expires_at` | string | No | The first accepted guard-query deadline. Required for an urgent query and omitted for a durable request notice. The format is `date-time`. |
| `type` | `"chat_join_request"` | Yes | The service or update type selecting this payload. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
