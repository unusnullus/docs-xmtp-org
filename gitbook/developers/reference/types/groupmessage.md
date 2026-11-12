# GroupMessage

A group message already routed to this bot. An anonymous human uses sender_chat and no fabricated sender_user. Bot-authored content is isolated from other bots. Internal routing cursors and human callback_context are not Bot API message fields.

You receive one of the following forms. Required fields apply within each form.

- `kind` is `"content"`: [GroupContentMessage](groupcontentmessage.md).

- `kind` is `"service"`: [GroupServiceMessage](groupservicemessage.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
