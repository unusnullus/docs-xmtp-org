# ChecklistTitle

The checklist heading and its formatting entities.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `entities` | Array of [ChecklistEntity](checklistentity.md) | Yes | These define formatting with offsets and lengths measured in UTF-16 code units. In requests, use these instead of format. |
| `text` | string | Yes | The heading displayed above the checklist tasks. You can use up to 255 UTF-16 code units after markup is parsed. The minimum length is 1 character. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
