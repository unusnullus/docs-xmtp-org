# RichReference

A named reference that links in the rich message can target.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `blocks` | Array of [RichBlock](richblock.md) | Yes | These are the rich content blocks in display order. |
| `reference_name` | string | Yes | This is the name of a reference defined within this rich message. |
| `type` | `"reference"` | Yes | The rich content node type is reference. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
