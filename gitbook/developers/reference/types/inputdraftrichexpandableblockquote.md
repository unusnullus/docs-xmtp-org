# InputDraftRichExpandableBlockquote

A quoted text block that the user can expand. Use this node in a temporary rich draft.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `credit` | [InputDraftRichText](inputdraftrichtext.md) | No | This is the attribution displayed with the quotation or media. |
| `text` | [InputDraftRichText](inputdraftrichtext.md) | Yes | The quoted text in this block. |
| `type` | `"expandable_blockquote"` | Yes | The rich content node type is expandable_blockquote. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
