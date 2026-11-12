# GroupRequestChat

The request_chat action fields. A reply-keyboard button also has its normal text/presentation fields. Filters are evaluated by the server; requested bot rights must be a subset of the user's rights. Admission precedes sharing and can succeed even if sharing later fails. Encrypted chats are never eligible.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `bot_admin_rights` | [GroupAdminRights](groupadminrights.md) | No | The requested bot administrator rights. They must be a subset of the user's rights. |
| `bot_is_member` | boolean | No | Whether the selected chat must already contain this bot. |
| `chat_has_username` | boolean | No | Whether the selected chat must have a public username. |
| `chat_is_channel` | boolean | Yes | Whether selection is restricted to channels rather than groups. |
| `chat_is_created` | boolean | No | Whether the selecting user must own the selected chat. |
| `chat_is_forum` | boolean | No | Whether the selected group must be a forum. |
| `request_id` | string | Yes | Use this identifier to correlate a response or a users_shared event with its request. The minimum length is 1 character. The maximum length is 64 characters. |
| `request_photo` | boolean | No | Set this to true to request profile photos when the user shares accounts. |
| `request_title` | boolean | No | Whether to include the selected chat's title in chat_shared. |
| `request_username` | boolean | No | Set this to true to request handles when the user shares accounts. |
| `type` | `"request_chat"` | Yes | The service or update type selecting this payload. |
| `user_admin_rights` | [GroupAdminRights](groupadminrights.md) | No | The administrator rights the selecting user must currently hold. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
