# InputChecklistTask

One task to create in a checklist, with an identifier unique within that checklist.

Only the listed fields are allowed. Do not supply `text_format` and `text_entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `id` | string | Yes | Choose a task identifier that is unique within the checklist. The value must match `^[A-Za-z0-9_-]{1,64}$`. The minimum length is 1 character. The maximum length is 64 characters. The following condition must not hold: the value has the type any JSON value. |
| `text` | string | Yes | The text of this checklist task. You can use up to 100 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `text_entities` | Array of [InputChecklistEntity](inputchecklistentity.md) | No | These define explicit formatting for the text. Use these instead of text_format. |
| `text_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse this text. Use this instead of text_entities. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
