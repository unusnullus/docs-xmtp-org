# InputDraftRichSlideshow

A sequence of media items displayed as a slideshow. Use this node in a temporary rich draft.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `blocks` | Array of [InputDraftRichPhoto](inputdraftrichphoto.md) or [InputDraftRichVideo](inputdraftrichvideo.md) or [InputDraftRichAnimation](inputdraftrichanimation.md) or [InputDraftRichAudio](inputdraftrichaudio.md) or [InputDraftRichVoice](inputdraftrichvoice.md) or [InputDraftRichDocument](inputdraftrichdocument.md) | Yes | These are the rich content blocks in display order. The minimum is 1 item. |
| `caption` | [InputDraftRichCaption](inputdraftrichcaption.md) | No | This is the caption displayed with the media. |
| `type` | `"slideshow"` | Yes | The rich content node type is slideshow. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
