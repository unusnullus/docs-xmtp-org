# InputDraftRichPreformatted

A code or preformatted text block, with optional syntax highlighting. Use this node in a temporary rich draft.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `language` | string | No | This is the language used to highlight a preformatted code block. The maximum length is 64 characters. |
| `text` | string | Yes | The literal code or preformatted text. The language field selects syntax highlighting. |
| `type` | `"preformatted"` | Yes | The rich content node type is preformatted. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
