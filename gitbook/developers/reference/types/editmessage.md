# EditMessage

The content, caption, live location, or inline keyboard changes to apply to a message. Choose one content change: replace content, edit caption fields, or update live_location. You can also change the inline keyboard. Caption edits preserve the media. Omit caption to keep its text, and change only caption_above_media to preserve its entities. You can place captions above photos, videos, and animations. Omit reply_markup to keep the keyboard, or set its rows to [] to remove buttons. You cannot change open_reply. An edit that leaves the message unchanged keeps its revision. A change increments the revision once.

Only the listed fields are allowed. Do not supply `content` and `live_location` together. If `link_preview` is present, also include `content`. Do not supply `caption_format` and `caption_entities` together. If any of `caption`, `caption_format`, `caption_entities`, or `caption_above_media` is present, omit `content` and `live_location`. Include at least one of `content`, `reply_markup`, `live_location`, `caption`, `caption_format`, `caption_entities`, or `caption_above_media`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | string | No | This is the caption displayed with the media. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `caption_above_media` | boolean | No | This is true when the caption appears above the media. |
| `caption_entities` | Array of [InputEntity](inputentity.md) | No | These define explicit caption formatting. In requests, use these instead of caption_format. |
| `caption_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the caption. Omit caption_entities when you set this field. |
| `content` | [InputEditContent](inputeditcontent.md) | No | This contains the message content. The type field selects its variant. |
| `expected_revision` | string | No | Set the message revision that you expect to edit. A different current revision returns revision_conflict. The value must match `^[1-9][0-9]*$`. |
| `link_preview` | [InputLinkPreview](inputlinkpreview.md) or null | No | This contains link preview settings or the resulting preview metadata. |
| `live_location` | [EditLiveLocation](editlivelocation.md) | No | Supply new coordinates and optional settings for an active live location. |
| `reply_markup` | [InputInlineKeyboard](inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
