# InputDraftRichInlineAnchorLink

An inline link to a named anchor in this message. Use this node in a temporary rich draft.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `anchor_name` | string | Yes | This is the name of the anchor within this rich message. |
| `text` | [InputDraftRichText](inputdraftrichtext.md) | Yes | The link text that points to a named anchor in this rich message. |
| `type` | `"anchor_link"` | Yes | The rich content node type is anchor_link. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
