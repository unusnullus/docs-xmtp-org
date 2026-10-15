# InputRich

Structured rich message content, supplied as blocks, HTML, or ARX Markdown.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `rich` | object | Yes | This contains structured rich message content. Provide exactly one of blocks, html, or markdown. A rich message can contain up to 500 blocks, a nesting depth of 16, 32,768 UTF-16 code units, 50 media occurrences, 100 buttons, and 20 table columns. Media dictionary IDs must be unique. Use structured blocks to include interactive buttons. Only the listed fields are allowed. Include exactly one of `blocks`, `html`, or `markdown`. |
| `rich.blocks` | Array of [InputRichBlock](inputrichblock.md) | No | These are the rich content blocks in display order. |
| `rich.html` | string | No | Write this rich message content in the supported HTML format. |
| `rich.is_rtl` | boolean | No | This is true when rich content is laid out from right to left. |
| `rich.markdown` | string | No | Write this rich message content in ARX Markdown. |
| `rich.media` | Array of object | No | This contains attached media or the named media sources for a rich message. The maximum is 50 items. |
| `rich.media[]` | object | Per item | Only the listed fields are allowed. |
| `rich.media[].file` | [InputFile](inputfile.md) | Yes | The source of this rich media item. Supply an existing file_id, the attach name of a binary part in this request, or a public URL where URL imports are supported. |
| `rich.media[].id` | string | Yes | Choose a unique name for this media source within the rich message. Refer to it from a block's media_id. |
| `rich.skip_entity_detection` | boolean | No | Set this to true to disable automatic entity detection in rich content. |
| `type` | `"rich"` | Yes | The rich content node type is rich. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
