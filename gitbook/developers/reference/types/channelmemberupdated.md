# ChannelMemberUpdated

A change to your bot's membership in a channel. It includes the previous and new membership and identifies the person, bot or automatic action responsible.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `at` | string | Yes | The accepted transition time in RFC 3339 format. The format is `date-time`. |
| `chat` | [ChannelChat](channelchat.md) | Yes | The channel whose membership changed. |
| `new` | [ChannelMember](channelmember.md) | Yes | The bot's membership immediately after the change. |
| `old` | [ChannelMember](channelmember.md) | Yes | The bot's membership immediately before the change. |
| `service_kind` | `"autopost_migration"` or `"chat_became_private"` or `"chat_deleted"` | No | The explicit actorless channel service cause. When present, user must be absent. |
| `user` | [UserIdentity](useridentity.md) | No | The person or bot responsible for this change. Omitted for an automatic service. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
