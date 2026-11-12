# GroupInviteLink

A bot's own invite link. invite_code is the hash segment used by Bot API paths; the human API's internal link_id is not exposed. Another creator's links cannot be managed by this bot.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `creates_join_request` | boolean | Yes | Whether this invitation creates join requests. It cannot be combined with a member limit. |
| `creator` | [UserIdentity](useridentity.md) | Yes | The account that created this invitation. |
| `expires_at` | string | No | The invitation expiration time in RFC 3339 format; omitted for no fixed expiration. The format is `date-time`. |
| `invite_code` | string | Yes | The 22-character invitation hash used by Bot API invitation paths. The value must match `^[A-Za-z0-9_-]{22}$`. |
| `invite_link` | string | Yes | An invitation available in this context. A bot can manage only invitations it created. The format is `uri`. |
| `is_primary` | boolean | Yes | Whether this is the creator's primary invitation. |
| `is_revoked` | boolean | Yes | Whether this invitation has been revoked. |
| `member_limit` | integer | No | The maximum number of concurrent members admitted by this invitation, from 1 to 99999. |
| `name` | string | No | The invitation's optional display label. The maximum length is 32 characters. |
| `pending_join_request_count` | integer | No | The number of pending requests created through this invitation. The minimum is 0. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
