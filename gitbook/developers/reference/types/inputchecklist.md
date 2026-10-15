# InputChecklist

The title, tasks, and collaboration settings for a new checklist.

Only the listed fields are allowed. Do not supply `title_format` and `title_entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `others_can_add_tasks` | boolean | No | This is true when other users can add tasks to this checklist. The default is `false`. |
| `others_can_mark_tasks_as_done` | boolean | No | This is true when other users can complete or reopen checklist tasks. The default is `false`. |
| `tasks` | Array of [InputChecklistTask](inputchecklisttask.md) | Yes | These are the checklist tasks in display order. The minimum is 1 item. The maximum is 30 items. Each item must be unique. Each item's `id` must be unique. |
| `title` | string | Yes | This is the title displayed for the object. You can use up to 255 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `title_entities` | Array of [InputChecklistEntity](inputchecklistentity.md) | No | These define explicit formatting for the checklist title. Use these instead of title_format. |
| `title_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the checklist title. Use this instead of title_entities. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
