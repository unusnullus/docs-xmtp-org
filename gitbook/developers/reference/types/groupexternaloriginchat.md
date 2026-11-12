# GroupExternalOriginChat

GroupExternalOriginChat is a group contract model. Availability is controlled by the evaluated group feature flags.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `author_signature` | string | No | This is the signature displayed for the author of the original message. |
| `chat` | [GroupSenderChat](groupsenderchat.md) | Yes | This identifies the chat associated with the message or event. |
| `sent_at` | string | Yes | This records when the message was sent, in RFC 3339 format. The format is `date-time`. |
| `type` | `"chat"` | Yes | The service or update type selecting this payload. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
