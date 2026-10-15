# EntityTextLink

A UTF-16 text span representing a text link.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `length` | integer | Yes | This is the length of the entity in UTF-16 code units. The minimum is 1. |
| `offset` | integer | Yes | This is the zero-based start of the entity in UTF-16 code units. The minimum is 0. |
| `type` | `"text_link"` | Yes | The formatting entity type is text_link. |
| `url` | string | Yes | The link opened when the user activates this button or text link. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
