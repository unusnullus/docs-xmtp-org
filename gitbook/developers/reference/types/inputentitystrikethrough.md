# InputEntityStrikethrough

A UTF-16 text span representing strikethrough text.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `length` | integer | Yes | This is the length of the entity in UTF-16 code units. The minimum is 1. |
| `offset` | integer | Yes | This is the zero-based start of the entity in UTF-16 code units. The minimum is 0. |
| `type` | `"strikethrough"` | Yes | The formatting entity type is strikethrough. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
