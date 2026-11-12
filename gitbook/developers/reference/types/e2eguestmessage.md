# E2eGuestMessage

An E2E Guest invocation. All disclosed message and quote content is client_asserted and unverified by the server. The bot receives only this disclosure, with no participants, title, history or future updates. guest_query_id is its only identity; message_id is absent. Different callers have different pseudonymous chat IDs.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `answer_deadline_at` | string | Yes | The original answer deadline, unchanged by retries. The format is `date-time`. |
| `chat` | object | Yes | This identifies the chat associated with the message or event. |
| `chat.id` | string | Yes | The identifier of this object. The value must match `^[1-9][0-9]*$`. |
| `chat.kind` | `"e2e_guest"` | Yes | A caller-scoped pseudonymous Guest chat, without membership or history access. |
| `client_asserted` | object | Yes | Unverified declarations by the caller; these fields grant no history or membership access. |
| `client_asserted.chat_type` | `"e2e_private"` or `"e2e_group"` | Yes | The caller's unverified claim about the E2E chat type. |
| `client_asserted.reply_to` | [GuestDisclosedQuote](guestdisclosedquote.md) | No | This identifies the reply target and optional quote for the message. |
| `guest_caller_user` | [User](user.md) | Yes | The authenticated caller's public profile, without any other participant identity. |
| `guest_query_id` | string | Yes | The sole identity of this Guest request. Answer it with guest_queries.answer; there is no message_id or history lookup. The value must match `^[0-9a-f]{32}$`. |
| `is_from_offline` | `false` | Yes | Guest requests use their original short answer window. |
| `message` | [GuestDisclosedContent](guestdisclosedcontent.md) | Yes | Content asserted by the caller, not verified against an E2E history. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
