# GroupReply

One level of an admitted local quote, a content-free message_id reference for an edit, or an admitted external source summary. External private origins disclose no source chat or message IDs. No variant grants access to the original content; nested replies and ephemeral originals are omitted.

You receive one of the following forms. Required fields apply within each form.

**When `message` has the type [GroupQuotedMessage](groupquotedmessage.md):**

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `message` | [GroupQuotedMessage](groupquotedmessage.md) | Yes | This is the message associated with the event or reply. |

**When `message_id` has the type string:**

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `message_id` | string | Yes | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

**When `external` has the type [GroupExternalReplyInfo](groupexternalreplyinfo.md) and `quote` is omitted or has the type [Quote](quote.md):**

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `external` | [GroupExternalReplyInfo](groupexternalreplyinfo.md) | Yes | This describes a reply target whose full message is unavailable here. |
| `quote` | [Quote](quote.md) | No | This is the quoted portion of the message that you are replying to. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
