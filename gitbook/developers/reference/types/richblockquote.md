# RichBlockquote

A block containing quoted rich content.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `blocks` | Array of [RichBlock](richblock.md) | Yes | These are the rich content blocks in display order. |
| `credit` | [RichText](richtext.md) | No | This is the attribution displayed with the quotation or media. |
| `type` | `"blockquote"` | Yes | The rich content node type is blockquote. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
