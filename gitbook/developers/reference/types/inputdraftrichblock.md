# InputDraftRichBlock

One supported rich content block. Use this node in a temporary rich draft.

Supply exactly one of the following forms. Required fields apply within each form.

- When `type` is `"heading"`, use [InputDraftRichHeading](inputdraftrichheading.md).

- When `type` is `"paragraph"`, use [InputDraftRichParagraph](inputdraftrichparagraph.md).

- When `type` is `"footer"`, use [InputDraftRichFooter](inputdraftrichfooter.md).

- When `type` is `"divider"`, use [InputDraftRichDivider](inputdraftrichdivider.md).

- When `type` is `"preformatted"`, use [InputDraftRichPreformatted](inputdraftrichpreformatted.md).

- When `type` is `"math"`, use [InputDraftRichMath](inputdraftrichmath.md).

- When `type` is `"blockquote"`, use [InputDraftRichBlockquote](inputdraftrichblockquote.md).

- When `type` is `"expandable_blockquote"`, use [InputDraftRichExpandableBlockquote](inputdraftrichexpandableblockquote.md).

- When `type` is `"pull_quote"`, use [InputDraftRichPullQuote](inputdraftrichpullquote.md).

- When `type` is `"details"`, use [InputDraftRichDetails](inputdraftrichdetails.md).

- When `type` is `"reference"`, use [InputDraftRichReference](inputdraftrichreference.md).

- When `type` is `"anchor"`, use [InputDraftRichAnchor](inputdraftrichanchor.md).

- When `type` is `"list"`, use [InputDraftRichList](inputdraftrichlist.md).

- When `type` is `"table"`, use [InputDraftRichTable](inputdraftrichtable.md).

- When `type` is `"photo"`, use [InputDraftRichPhoto](inputdraftrichphoto.md).

- When `type` is `"video"`, use [InputDraftRichVideo](inputdraftrichvideo.md).

- When `type` is `"animation"`, use [InputDraftRichAnimation](inputdraftrichanimation.md).

- When `type` is `"audio"`, use [InputDraftRichAudio](inputdraftrichaudio.md).

- When `type` is `"voice"`, use [InputDraftRichVoice](inputdraftrichvoice.md).

- When `type` is `"document"`, use [InputDraftRichDocument](inputdraftrichdocument.md).

- When `type` is `"collage"`, use [InputDraftRichCollage](inputdraftrichcollage.md).

- When `type` is `"slideshow"`, use [InputDraftRichSlideshow](inputdraftrichslideshow.md).

- When `type` is `"map"`, use [InputDraftRichMap](inputdraftrichmap.md).

- When `type` is `"buttons"`, use [InputDraftRichButtons](inputdraftrichbuttons.md).

**When `type` is `"thinking"`:**

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `text` | [InputDraftRichText](inputdraftrichtext.md) | Yes | The thinking text displayed while your bot generates the rich draft. |
| `type` | `"thinking"` | Yes | The rich content node type is thinking. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
