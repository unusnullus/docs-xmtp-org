# ChecklistTasksDoneService

A service event identifying checklist tasks marked complete or incomplete.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `checklist_message_id` | string | Yes | This is the ID of the message containing the checklist. The value must match `^[1-9][0-9]*$`. |
| `marked_done_task_ids` | Array of string | Yes | These are the IDs of the checklist tasks marked as complete. The maximum is 30 items. Each item must be unique. |
| `marked_done_task_ids[]` | string | Per item | The value must match `^[A-Za-z0-9_-]{1,64}$`. The minimum length is 1 character. The maximum length is 64 characters. The following condition must not hold: the value has the type any JSON value. |
| `marked_not_done_task_ids` | Array of string | Yes | These are the IDs of the checklist tasks marked as incomplete. The maximum is 30 items. Each item must be unique. |
| `marked_not_done_task_ids[]` | string | Per item | The value must match `^[A-Za-z0-9_-]{1,64}$`. The minimum length is 1 character. The maximum length is 64 characters. The following condition must not hold: the value has the type any JSON value. |
| `type` | `"checklist_tasks_done"` | Yes | The service event type is checklist_tasks_done. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
