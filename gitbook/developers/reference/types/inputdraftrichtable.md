# InputDraftRichTable

A rich table containing rows and cells. Use this node in a temporary rich draft.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | [InputDraftRichText](inputdraftrichtext.md) | No | This is the caption displayed with the media. |
| `is_bordered` | boolean | No | This is true when the table displays cell borders. |
| `is_compact` | boolean | No | This is true when the table uses compact spacing. |
| `is_striped` | boolean | No | This is true when the table displays alternating row backgrounds. |
| `rows` | Array of Array of object | Yes | These are the rows of the keyboard or rich table, in display order. The minimum is 1 item. |
| `rows[][]` | object | Per item | Only the listed fields are allowed. |
| `rows[][].align` | `"left"` or `"center"` or `"right"` | No | This controls the horizontal alignment of a cell or button row. |
| `rows[][].colspan` | integer | No | This is the number of table columns that this cell spans. The minimum is 1. The maximum is 20. |
| `rows[][].is_header` | boolean | No | This is true when this table cell is a header. |
| `rows[][].rowspan` | integer | No | This is the number of table rows that this cell spans. The minimum is 1. The maximum is 500. |
| `rows[][].text` | [InputDraftRichText](inputdraftrichtext.md) | No | The inline text displayed in this table cell. |
| `rows[][].valign` | `"top"` or `"middle"` or `"bottom"` | No | This controls the vertical alignment of the table cell. |
| `type` | `"table"` | Yes | The rich content node type is table. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
