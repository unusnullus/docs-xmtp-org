# RichInlineTextMention

An inline mention linked to a user ID.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `text` | [RichText](richtext.md) | Yes | The text displayed for the linked user mention. |
| `type` | `"text_mention"` | Yes | The rich content node type is text_mention. |
| `user` | [User](user.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
