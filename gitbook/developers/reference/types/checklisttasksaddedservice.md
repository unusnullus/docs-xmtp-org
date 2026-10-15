# ChecklistTasksAddedService

A service event identifying tasks added to a checklist.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `checklist_message_id` | string | Yes | This is the ID of the message containing the checklist. The value must match `^[1-9][0-9]*$`. |
| `tasks` | Array of [ChecklistTask](checklisttask.md) | Yes | These are the checklist tasks in display order. The minimum is 1 item. The maximum is 30 items. |
| `type` | `"checklist_tasks_added"` | Yes | The service event type is checklist_tasks_added. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
