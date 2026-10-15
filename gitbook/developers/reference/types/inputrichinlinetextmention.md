# InputRichInlineTextMention

An inline mention linked to a user ID. Use this node when sending rich content.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `text` | [InputRichText](inputrichtext.md) | Yes | The text displayed for the linked user mention. |
| `type` | `"text_mention"` | Yes | The rich content node type is text_mention. |
| `user_id` | string | Yes | This is the user ID, encoded as a decimal string. A user ID differs from a private chat ID. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
