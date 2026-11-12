# PersonalChannelPosts

A bounded page of visible personal public channel posts with current channel metadata.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `personal_chat` | [PersonalChannelChat](personalchannelchat.md) or null | Yes | The user's currently visible personal public channel, or null when no eligible channel is available. |
| `posts` | Array of [Message](message.md) | Yes | Up to 20 visible posts from the user's personal public channel, newest first. An unavailable personal channel has an empty list. The maximum is 20 items. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
