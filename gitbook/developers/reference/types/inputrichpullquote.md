# InputRichPullQuote

A prominent quoted text block with optional credit. Use this node when sending rich content.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `credit` | [InputRichText](inputrichtext.md) | No | This is the attribution displayed with the quotation or media. |
| `text` | [InputRichText](inputrichtext.md) | Yes | The quoted text in this block. |
| `type` | `"pull_quote"` | Yes | The rich content node type is pull_quote. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
