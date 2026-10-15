# RichDetails

A collapsible section with a summary and nested content.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `blocks` | Array of [RichBlock](richblock.md) | Yes | These are the rich content blocks in display order. |
| `is_open` | boolean | No | This is true when the details block starts expanded. |
| `summary` | [RichText](richtext.md) | Yes | This is the text displayed on the collapsible details block. |
| `type` | `"details"` | Yes | The rich content node type is details. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
