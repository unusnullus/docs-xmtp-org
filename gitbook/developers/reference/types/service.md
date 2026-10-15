# Service

A service event carried by a message. Its type identifies the event.

You receive one of the following forms. Required fields apply within each form.

- `type` is `"message_pinned"`: [MessagePinnedService](messagepinnedservice.md).

- `type` is `"users_shared"`: [UsersSharedService](userssharedservice.md).

- `type` is `"bot_owner_changed"`: [BotOwnerChangedService](botownerchangedservice.md).

- `type` is `"poll_option_added"`: [PollOptionAddedService](polloptionaddedservice.md).

- `type` is `"poll_option_deleted"`: [PollOptionDeletedService](polloptiondeletedservice.md).

- `type` is `"checklist_tasks_done"`: [ChecklistTasksDoneService](checklisttasksdoneservice.md).

- `type` is `"checklist_tasks_added"`: [ChecklistTasksAddedService](checklisttasksaddedservice.md).

- `type` is `"proximity_alert_triggered"`: [ProximityAlertService](proximityalertservice.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
