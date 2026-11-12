# GroupJoinRequest

GroupJoinRequest is a group contract model. Availability is controlled by the evaluated group feature flags.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `at` | string | Yes | This records the date and time of the event or displayed date, in RFC 3339 format. The format is `date-time`. |
| `bio` | string | No | The join requester's profile biography, when available. |
| `chat` | [GroupChat](groupchat.md) | Yes | This identifies the chat associated with the message or event. |
| `invite_link` | [GroupInviteLink](groupinvitelink.md) | No | An invitation available in this context. A bot can manage only invitations it created. |
| `query_id` | string | No | Opaque query supplied only to the assigned guard; it grants no group membership or private message permission. The value must match `^[A-Za-z0-9_-]{21}[AQgw]$`. The minimum length is 22 characters. The maximum length is 22 characters. The following condition must not hold: the value has the type `"AAAAAAAAAAAAAAAAAAAAAA"`. |
| `user` | [User](user.md) | Yes | This identifies the user associated with the event, mention, or private chat. |
| `user_chat_id` | string | Yes | The durable private chat ID for this requester and bot. Ordinary private-chat authorization still applies. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
