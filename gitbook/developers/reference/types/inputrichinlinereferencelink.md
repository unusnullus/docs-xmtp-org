# InputRichInlineReferenceLink

An inline link to a named reference in this message. Use this node when sending rich content.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `reference_name` | string | Yes | This is the name of a reference defined within this rich message. |
| `text` | [InputRichText](inputrichtext.md) | Yes | The link text that refers to the named reference in this rich message. |
| `type` | `"reference_link"` | Yes | The rich content node type is reference_link. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
