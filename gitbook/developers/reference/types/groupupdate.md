# GroupUpdate

Each group update contains exactly one payload matching type. Messages, edits, deletions, membership and join requests are available in Update when the group features are enabled for the receiving bot. Additional variants depend on their enabled features. Use allowed_updates to select update kinds. Receiving an update grants no additional group permissions.

You receive one of the following forms. Required fields apply within each form.

- `type` is `"message"`: [GroupUpdateMessage](groupupdatemessage.md).

- `type` is `"edited_message"`: [GroupUpdateEditedMessage](groupupdateeditedmessage.md).

- `type` is `"messages_deleted"`: [GroupUpdateMessagesDeleted](groupupdatemessagesdeleted.md).

- `type` is `"my_chat_member"`: [GroupUpdateMyChatMember](groupupdatemychatmember.md).

- `type` is `"chat_member"`: [GroupUpdateChatMember](groupupdatechatmember.md).

- `type` is `"chat_join_request"`: [GroupUpdateChatJoinRequest](groupupdatechatjoinrequest.md).

- `type` is `"message_reaction"`: [GroupUpdateMessageReaction](groupupdatemessagereaction.md).

- `type` is `"message_reaction_count"`: [GroupUpdateMessageReactionCount](groupupdatemessagereactioncount.md).

- `type` is `"poll"`: [GroupUpdatePoll](groupupdatepoll.md).

- `type` is `"poll_answer"`: [GroupUpdatePollAnswer](groupupdatepollanswer.md).

- `type` is `"callback_query"`: [GroupUpdateCallbackQuery](groupupdatecallbackquery.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
