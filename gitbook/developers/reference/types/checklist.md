# Checklist

The checklist title, tasks, and collaboration settings returned in a message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `others_can_add_tasks` | boolean | Yes | This is true when other users can add tasks to this checklist. |
| `others_can_mark_tasks_as_done` | boolean | Yes | This is true when other users can complete or reopen checklist tasks. |
| `tasks` | Array of [ChecklistTask](checklisttask.md) | Yes | These are the checklist tasks in display order. The minimum is 1 item. The maximum is 30 items. |
| `title` | [ChecklistTitle](checklisttitle.md) | Yes | This is the title displayed for the object. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
