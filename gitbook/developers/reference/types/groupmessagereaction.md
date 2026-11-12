# GroupMessageReaction

GroupMessageReaction is a group contract model. Availability is controlled by the evaluated group feature flags.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `actor_chat` | [GroupSenderChat](groupsenderchat.md) | No | The real group or channel identity of an anonymous reaction author. |
| `at` | string | Yes | This records the date and time of the event or displayed date, in RFC 3339 format. The format is `date-time`. |
| `chat` | [GroupChat](groupchat.md) | Yes | This identifies the chat associated with the message or event. |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `new_reactions` | Array of [Reaction](reaction.md) | Yes | These are the user's reactions after the change. |
| `old_reactions` | Array of [Reaction](reaction.md) | Yes | These are the user's reactions before the change. |
| `user` | [User](user.md) | No | This identifies the user associated with the event, mention, or private chat. |

You receive one of the following forms. Required fields apply within each form.

- The response includes `user`. The response omits `actor_chat`.

- The response includes `actor_chat`. The response omits `user`.

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
