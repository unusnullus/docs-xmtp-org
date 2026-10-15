# Rich

The rich content blocks returned in a message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `rich` | object | Yes | This contains structured rich message content. |
| `rich.blocks` | Array of [RichBlock](richblock.md) | Yes | These are the rich content blocks in display order. |
| `rich.is_rtl` | boolean | Yes | This is true when rich content is laid out from right to left. |
| `type` | `"rich"` | Yes | The rich content node type is rich. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
