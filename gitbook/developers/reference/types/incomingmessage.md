# IncomingMessage

A private payload or a group payload selected by chat.kind. Group delivery requires nexo_groups.enabled and nexo_groups.bots for the actual recipient; this type grants no access.

You receive one of the following forms. Required fields apply within each form.

- A group message already routed to this bot: [GroupMessage](groupmessage.md).

- A content message or a service message available to your bot: [Message](message.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
