# InputRichDetails

A collapsible section with a summary and nested content. Use this node when sending rich content.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `blocks` | Array of [InputRichBlock](inputrichblock.md) | Yes | These are the rich content blocks in display order. |
| `is_open` | boolean | No | This is true when the details block starts expanded. |
| `summary` | [InputRichText](inputrichtext.md) | Yes | This is the text displayed on the collapsible details block. |
| `type` | `"details"` | Yes | The rich content node type is details. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
