# GroupUpdateEditedMessage

GroupUpdateEditedMessage is a group contract model. Availability is controlled by the evaluated group feature flags.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | The creation time of this event in RFC 3339 format. The format is `date-time`. |
| `edited_message` | [GroupMessage](groupmessage.md) | Yes | You receive this message after an edit that your bot can observe. |
| `type` | `"edited_message"` | Yes | The service or update type selecting this payload. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
