# ChatMetadata

Current chat details visible to your bot, for a private chat, group or public channel.

You receive one of the following forms. Required fields apply within each form.

- `kind` is `"private"`: [ChatFullInfo](chatfullinfo.md).

- `kind` is `"group"`: [GroupChatFullInfo](groupchatfullinfo.md).

- `is_forum` is `false` and `kind` is `"channel"`: [ChannelChatFullInfo](channelchatfullinfo.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
