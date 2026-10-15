# InputDraftRich

Rich content for a temporary draft, supplied as blocks, HTML, or ARX Markdown. Provide exactly one of blocks, html, or markdown. A rich message can contain up to 500 blocks, a nesting depth of 16, 32,768 UTF-16 code units, 50 media occurrences, 100 buttons, and 20 table columns. Media dictionary IDs must be unique. Use structured blocks to include interactive buttons.

Only the listed fields are allowed. Include exactly one of `blocks`, `html`, or `markdown`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `blocks` | Array of [InputDraftRichBlock](inputdraftrichblock.md) | No | These are the rich content blocks in display order. |
| `html` | string | No | Write this rich message content in the supported HTML format. |
| `is_rtl` | boolean | No | This is true when rich content is laid out from right to left. |
| `markdown` | string | No | Write this rich message content in ARX Markdown. |
| `media` | Array of object | No | This contains attached media or the named media sources for a rich message. The maximum is 50 items. |
| `media[]` | object | Per item | Only the listed fields are allowed. |
| `media[].file` | [InputDraftFile](inputdraftfile.md) | Yes | An existing file available to your bot. Draft media can reference file_id only; uploads and URL imports are rejected. |
| `media[].id` | string | Yes | The identifier of this object. |
| `skip_entity_detection` | boolean | No | Set this to true to disable automatic entity detection in rich content. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
