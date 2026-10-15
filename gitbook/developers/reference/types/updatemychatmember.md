# UpdateMyChatMember

An update carrying a change to your bot's chat membership.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | This records when the bot membership update was created, in RFC 3339 format. The format is `date-time`. |
| `my_chat_member` | [MemberUpdated](memberupdated.md) | Yes | This describes a change to your bot's membership in a chat. |
| `type` | `"my_chat_member"` | Yes | The update type is my_chat_member. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
