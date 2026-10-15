# StopPoll

The optional inline keyboard replacement to apply when closing a poll. Close an open poll authored by your bot. You can replace its inline keyboard while preserving open_reply. The poll closure and keyboard change succeed together. Reusing the idempotency key returns the final poll from the original request. A separate close request returns poll_closed.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `reply_markup` | [InputInlineKeyboard](inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
