# InputRichSlideshow

A sequence of media items displayed as a slideshow. Use this node when sending rich content.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `blocks` | Array of [InputRichPhoto](inputrichphoto.md) or [InputRichVideo](inputrichvideo.md) or [InputRichAnimation](inputrichanimation.md) or [InputRichAudio](inputrichaudio.md) or [InputRichVoice](inputrichvoice.md) or [InputRichDocument](inputrichdocument.md) | Yes | These are the rich content blocks in display order. The minimum is 1 item. |
| `caption` | [InputRichCaption](inputrichcaption.md) | No | This is the caption displayed with the media. |
| `type` | `"slideshow"` | Yes | The rich content node type is slideshow. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
