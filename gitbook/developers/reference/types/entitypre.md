# EntityPre

A UTF-16 text span representing preformatted code.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `language` | string | No | This is the language used to highlight a preformatted code block. The maximum length is 64 characters. |
| `length` | integer | Yes | This is the length of the entity in UTF-16 code units. The minimum is 1. |
| `offset` | integer | Yes | This is the zero-based start of the entity in UTF-16 code units. The minimum is 0. |
| `type` | `"pre"` | Yes | The formatting entity type is pre. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
