# SendAlbum

The media items and delivery settings for a new album. Send 2 to 10 items together. You can mix photos, videos, and live photos. An audio or document album must contain only its own kind. The messages have consecutive IDs and share a media_group_id. The request uses one global send unit per item and one chat send unit per album.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `effect_id` | string | No | Use a message effect identifier returned by message_effects.list. |
| `items` | Array of [AlbumItem](albumitem.md) | Yes | These are the items in their returned or requested order. The minimum is 2 items. The maximum is 10 items. |
| `protect_content` | boolean | No | Set this to true to protect the new message against copying and forwarding. |
| `reply_to` | [InputReply](inputreply.md) | No | This identifies the reply target and optional quote for the message. |
| `silent` | boolean | No | Set this to true to send or pin the message without a notification sound. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
