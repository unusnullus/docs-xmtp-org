# GroupExternalReplyHidden

A private or otherwise hidden source summary. No source chat/message ID, name, text, media reference or nested message is exposed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `content_type` | string | Yes | This is the content type of the original message. The value must match `^[a-z_]{1,64}$`. |
| `has_media_spoiler` | boolean | Yes | This is true when the original media is hidden behind a spoiler. |
| `origin` | [GroupExternalOriginHidden](groupexternaloriginhidden.md) | Yes | This identifies the origin of the original message. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
