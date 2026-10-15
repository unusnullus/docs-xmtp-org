# RichSlideshow

A sequence of media items displayed as a slideshow.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `blocks` | Array of [RichPhoto](richphoto.md) or [RichVideo](richvideo.md) or [RichAnimation](richanimation.md) or [RichAudio](richaudio.md) or [RichVoice](richvoice.md) or [RichDocument](richdocument.md) | Yes | These are the rich content blocks in display order. The minimum is 1 item. |
| `caption` | [RichCaption](richcaption.md) | No | This is the caption displayed with the media. |
| `type` | `"slideshow"` | Yes | The rich content node type is slideshow. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
