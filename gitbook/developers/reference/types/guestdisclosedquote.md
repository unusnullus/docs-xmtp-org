# GuestDisclosedQuote

An optional client-asserted quote. It is not evidence that the bot or another person authored that text.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `author` | `"caller"` or `"bot"` or `"other"` | Yes | Unverified author category. No identity of another person is disclosed. |
| `entities` | Array of [Entity](entity.md) | Yes | Formatting asserted for the quote. |
| `text` | string | Yes | The quoted text asserted by the caller, without a history reference. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
