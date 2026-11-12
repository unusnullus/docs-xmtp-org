# UpdateBusinessConnection

An update reporting the connected account state or permissions. It grants no permission to send a private message. An update reporting the connected account state or permissions. It grants no permission to send a private message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `business_connection` | [BusinessConnection](businessconnection.md) | Yes | The connected account and the accepted connection state or permissions. |
| `created_at` | string | Yes | UTC time when this connection update was created. The format is `date-time`. |
| `type` | `"business_connection"` | Yes | The update type is business_connection. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
