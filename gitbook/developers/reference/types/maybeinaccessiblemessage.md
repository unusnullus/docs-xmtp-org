# MaybeInaccessibleMessage

A message returned with its content when accessible, or only its identity when unavailable.

You receive one of the following forms. Required fields apply within each form.

- A content message or a service message available to your bot: [Message](message.md).

- `kind` is `"inaccessible"`: [InaccessibleMessage](inaccessiblemessage.md).

- `is_ephemeral` is `false` and `kind` is `"content"`: [GroupContentMessage](groupcontentmessage.md).

- `is_ephemeral` is `false` and `kind` is `"service"`: [GroupServiceMessage](groupservicemessage.md).

- `kind` is `"inaccessible"`: [GroupInaccessibleMessage](groupinaccessiblemessage.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
