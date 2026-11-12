# InlineQuery

An inline query with its original 30-second answer deadline. Saved-header queries expose either client_asserted E2E scope or server-verified chat_type=group. No group identity, membership or message-send grant is exposed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_type` | `"sender"` or `"private"` or `"group"` or `"channel"` | No | The server-verified chat kind. End-to-end contexts use client_asserted instead. |
| `client_asserted` | object | No | Client-supplied end-to-end chat context. It does not establish a group identity, membership, or permission to send messages. |
| `client_asserted.chat_type` | `"e2e_private"` or `"e2e_group"` or `"e2e_channel"` | Yes | The end-to-end chat kind reported by the client, without server-verified group identity or membership. |
| `expires_at` | string | Yes | The expiration timestamp in RFC 3339 format; null is accepted only where this field is nullable. The format is `date-time`. |
| `id` | string | Yes | The identifier of this object. The value must match `^[A-Za-z0-9_-]{21}[AQgw]$`. The minimum length is 22 characters. The maximum length is 22 characters. The following condition must not hold: the value has the type `"AAAAAAAAAAAAAAAAAAAAAA"`. |
| `offset` | string | Yes | This is the start of this entity in UTF-16 code units, or the position associated with an error. The maximum length is 128 characters. |
| `query` | string | Yes | The user's search text, up to 256 characters. |
| `user` | [User](user.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

You receive one of the following forms. Required fields apply within each form.

- The response includes `chat_type`. The response omits `client_asserted`.

- The response includes `client_asserted`. The response omits `chat_type`.

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
