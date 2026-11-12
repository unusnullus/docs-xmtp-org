# GroupExternalOriginUser

GroupExternalOriginUser is a group contract model. Availability is controlled by the evaluated group feature flags.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `sent_at` | string | Yes | This records when the message was sent, in RFC 3339 format. The format is `date-time`. |
| `type` | `"user"` | Yes | The service or update type selecting this payload. |
| `user` | [User](user.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
