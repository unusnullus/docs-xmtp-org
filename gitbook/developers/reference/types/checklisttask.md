# ChecklistTask

A checklist task with its identifier, label, and completion state.

If `completed_at` is present, the response also includes `completed_by_user`. If `completed_by_user` is present, the response also includes `completed_at`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `completed_at` | string | No | This records when the task was completed, in RFC 3339 format. The format is `date-time`. |
| `completed_by_user` | [User](user.md) | No | This identifies the user who marked the task as complete. |
| `id` | string | Yes | The task identifier, unique within this checklist. The value must match `^[A-Za-z0-9_-]{1,64}$`. The minimum length is 1 character. The maximum length is 64 characters. The following condition must not hold: the value has the type any JSON value. |
| `text` | [ChecklistTaskText](checklisttasktext.md) | Yes | The formatted task label displayed in the checklist. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
