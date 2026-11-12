# GroupQuotedMessage

GroupQuotedMessage is a group contract model. Availability is controlled by the evaluated group feature flags.

You receive one of the following forms. Required fields apply within each form.

- `kind` is `"content"`: [GroupQuotedContentMessage](groupquotedcontentmessage.md).

- `kind` is `"service"`: [GroupQuotedServiceMessage](groupquotedservicemessage.md).

- `kind` is `"inaccessible"`: [GroupInaccessibleMessage](groupinaccessiblemessage.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
