# RichInlineAnchor

A named anchor within inline text.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `anchor_name` | string | Yes | This is the name of the anchor within this rich message. |
| `text` | [RichText](richtext.md) | No | The text at the named anchor within this rich message. |
| `type` | `"anchor"` | Yes | The rich content node type is anchor. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
