# GroupService

GroupService is a group contract model. Availability is controlled by the evaluated group feature flags.

You receive one of the following forms. Required fields apply within each form.

- `type` is `"bot_privacy_changed"`: [GroupBotPrivacyChangedService](groupbotprivacychangedservice.md).

- A group lifecycle service: [GroupLifecycleService](grouplifecycleservice.md).

- `type` is `"topic_created"`: [TopicCreatedService](topiccreatedservice.md).

- `type` is `"topic_edited"`: [TopicEditedService](topiceditedservice.md).

- `type` is `"topic_closed"`: [TopicClosedService](topicclosedservice.md).

- `type` is `"topic_reopened"`: [TopicReopenedService](topicreopenedservice.md).

- `type` is `"general_topic_hidden"`: [GeneralTopicHiddenService](generaltopichiddenservice.md).

- `type` is `"general_topic_unhidden"`: [GeneralTopicUnhiddenService](generaltopicunhiddenservice.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
