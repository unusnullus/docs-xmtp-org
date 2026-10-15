# Update

An event delivered to your bot. Inspect type to select its event payload.

Every update has an `update_id`, a `created_at` timestamp, and a `type`. The type identifies the event payload. Ignore update types you do not recognize so a new event type does not stop your bot from processing known events.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |
| `created_at` | string | Yes | This records when the poll state update was created, in RFC 3339 format. The format is `date-time`. |
| `type` | string | Yes | The event type identifies which payload is present. |

You receive one of the following forms. Required fields apply within each form.

- `type` is `"poll"`: [UpdatePoll](updatepoll.md).

- `type` is `"poll_answer"`: [UpdatePollAnswer](updatepollanswer.md).

- `type` is `"callback_query"`: [UpdateCallbackQuery](updatecallbackquery.md).

- `type` is `"message"`: [UpdateMessage](updatemessage.md).

- `type` is `"edited_message"`: [UpdateEditedMessage](updateeditedmessage.md).

- `type` is `"my_chat_member"`: [UpdateMyChatMember](updatemychatmember.md).

- `type` is `"messages_deleted"`: [UpdateMessagesDeleted](updatemessagesdeleted.md).

- `type` is `"message_reaction"`: [UpdateMessageReaction](updatemessagereaction.md).

- `type` is `"stopped_message_generation"`: [UpdateStoppedMessageGeneration](updatestoppedmessagegeneration.md).

- `type` is `"updates_dropped"`: [UpdateUpdatesDropped](updateupdatesdropped.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
