---
description: "Format text, captions and rich messages with ARX Markdown, HTML or entities."
---

# Formatting

You can format a message with ARX Markdown, HTML or explicit entities. Use rich content when you need headings, tables or media in the same document. The response contains parsed text and entities or a structured rich document that you can read without parsing your original markup.

## Send formatted text <a href="#send-formatted-text" id="send-formatted-text"></a>

Set `content.format` to `plain`, `markdown` or `html` when you supply markup. Omit it for plain text. You can also use the formatting helpers below to build entities. This example makes the heading bold and disables the link preview.

Set `ARX_BOT_TOKEN`, `CHAT_ID` and `REQUEST_KEY` before running the example. Use a private chat that the user has started. See [Making requests](making-requests.md) for choosing keys and retrying requests.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, FormattedText } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
if (!token || !key || !chatId) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const result = await bot.messagesSend(chatId, {
		key,
		body: {
			content: FormattedText.join(
				FormattedText.bold("Meeting notes"),
				"\nhttps://example.com/notes",
			).build(),
			link_preview: { disabled: true },
		},
	});
	console.log(result);
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models, FormattedText

chat_id = os.environ["CHAT_ID"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.SendMessage(
        content=FormattedText.join(
            FormattedText.bold("Meeting notes"), "\nhttps://example.com/notes"
        ).content(),
        link_preview=models.InputLinkPreview(disabled=True),
    )
    result = bot.messages_send(
        chat_id,
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
    print(result)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, FormattedText, models, presence::Presence};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let heading = FormattedText::bold("Meeting notes")?;
    let text = FormattedText::join(&[(&heading).into(), "\nhttps://example.com/notes".into()])?;
    let request = models::SendMessage {
        link_preview: Presence::Value(models::InputLinkPreview {
            disabled: Some(true),
            ..Default::default()
        }),
        ..models::SendMessage::new(text.content()?)
    };
    let result = bot
        .messages_send(&chat_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

## Choose the formatting fields <a href="#choose-the-formatting-fields" id="choose-the-formatting-fields"></a>

| Text you are formatting | Input fields | Parsed text limit |
| --- | --- | --- |
| Text message | `content.text` with `format` or `entities` | 4,096 UTF-16 units |
| Media caption | `caption` with `caption_format` or `caption_entities` inside the media content | 1,024 UTF-16 units |
| Quote | `reply_to.quote.text` with `format` or `entities` | 1,024 UTF-16 units |
| Poll description | `description` with `description_format` or `description_entities` | 1,024 UTF-16 units |
| Quiz explanation | `explanation` with `explanation_format` or `explanation_entities` | 200 UTF-16 units and at most two line breaks |
| Checklist title | `title` with `title_format` or `title_entities` | 255 UTF-16 units |
| Checklist task | `text` with `text_format` or `text_entities` | 100 UTF-16 units |

For each field, choose markup or an entity array. Supplying both is invalid, even if the array is empty. Limits apply after parsing markup. Text messages must contain at least one UTF-16 unit; captions and quotes can be empty. An empty caption or quote is omitted from the returned message.

For caption-only edits, keep the caption fields at the top level of `messages.edit`. See [Files](files.md) for the edit request and [Polls and checklists](polls-and-checklists.md) for their content fields.

## UTF-16 offsets <a href="#utf-16-offsets" id="utf-16-offsets"></a>

Entity `offset` and `length` count UTF-16 units in the parsed text. In `🧭 North`, the emoji occupies two units and the space one, so `North` begins at offset 3. Its HTTP request body is:

```json
{
  "content": {
    "type": "text",
    "text": "🧭 North",
    "entities": [{"type":"bold","offset":3,"length":5}]
  }
}
```

JavaScript's `text.length`, Python's `len(text.encode("utf-16-le")) // 2`, and Rust's `text.encode_utf16().count()` give the required count. Byte counts, Unicode scalar counts and the number of visible characters can differ. Invalid UTF-8, unpaired surrogates and C0 controls other than newline and tab are rejected.

## Entity types <a href="#entity-types" id="entity-types"></a>

| Entity | How to use it |
| --- | --- |
| `bold`, `italic`, `underline`, `strikethrough`, `spoiler` | Apply a style to a span. |
| `code`, `pre` | Mark code. Only `pre` accepts `language`. Neither can contain another entity. |
| `blockquote`, `expandable_blockquote` | Mark quoted text. These entities cannot nest in one another. |
| `text_link` | Supply `url` for the destination. |
| `text_mention` | Supply `user_id`; the response contains the resolved `user`. |
| `date_time` | Supply `at` and `date_time_format` to display a time in the reader's locale and timezone. |
| `mention`, `hashtag`, `cashtag`, `bot_command`, `url`, `email`, `phone_number` | ARX detects these in text. Hashtags and cashtags can include `chat_username`. |

Spans can be separate or sit inside one another. Partial overlaps are invalid. Semantic entities, such as links and mentions, cannot contain one another.

Captions, poll descriptions and quiz explanations accept the supported entity set. Quotes and checklist text accept only `bold`, `italic`, `underline`, `strikethrough`, `spoiler` and `date_time`. Poll questions and options use plain text; omit their entity arrays or send empty arrays.

For `date_time_format`, use `r` by itself for relative time. Otherwise, combine `w`, one of `d` or `D`, and one of `t` or `T`, in that order. Each part is optional: `w`, `D`, `t` and `wDT` are all valid. Lowercase `d` and `t` select short forms; uppercase selects long forms. An empty format keeps the label text.

## ARX Markdown <a href="#arx-markdown" id="arx-markdown"></a>

| Input | Result |
| --- | --- |
| `**ready**` or `__ready__` | Bold |
| `*ready*` or `_ready_` | Italic |
| `++ready++` | Underline |
| `~~ready~~` | Strikethrough |
| `\|\|ready\|\|` | Spoiler |
| `` `ready` `` | Inline code |
| A fenced code block with a language | Preformatted text |
| `[Open](https://example.com)` | A text link |
| `[Member](aerx://user?id=42)` | A mention of user ID 42 |
| `![Meeting](aerx://time?at=2026-10-20T15:00:00Z&format=wDT)` | A local date and time |
| A line starting with `>` | A blockquote |
| A quote whose first line is `> [!expand]` | An expandable blockquote |

ARX Markdown follows CommonMark inline rules with these additions. Unclosed delimiters remain literal. Escape ASCII punctuation with a backslash when you need to display it. Inside code, escape backticks and backslashes. Line breaks remain unchanged.

## ARX HTML <a href="#arx-html" id="arx-html"></a>

For HTTP, set `format: html` and place the markup in `text`:

```json
{
  "content": {
    "type": "text",
    "text": "<b>Ready</b><br><aerx-time at=\"2026-10-20T15:00:00Z\" format=\"wDT\">Meeting</aerx-time>",
    "format": "html"
  }
}
```

| Purpose | Supported tags |
| --- | --- |
| Styles | `b`, `strong`, `i`, `em`, `u`, `ins`, `s`, `strike`, `del`, `span class="spoiler"`, `aerx-spoiler` |
| Links and times | `a href`, `aerx-time` |
| Code and quotes | `code`, `pre`, `blockquote` |
| Line breaks | `br` |

A blockquote can have `expandable`. A `pre` can contain `code class="language-python"`. Escape markup characters with `&lt;`, `&gt;`, `&amp;`, `&quot;`, `&apos;` or numeric character references.

Unknown or mismatched HTML tags return HTTP 400 `invalid_parameter` with `reason: format_parse_error`. When present, `error.details[0].offset` gives the UTF-16 position of the malformed input. Invalid HTML link targets return reasons such as `invalid_url`, `invalid_pseudo_link` or `invalid_user_id`, without an offset.

## Rich messages <a href="#rich-messages" id="rich-messages"></a>

Set `content.type` to `rich`. Inside `content.rich`, provide exactly one of `markdown`, `html` or `blocks`. For HTTP:

```json
{
  "content": {
    "type": "rich",
    "rich": {
      "markdown": "## Meeting plan\n\n- Review the draft\n- Book the room\n\n| Time | Task |\n| --- | --- |\n| 15:00 | Meeting |"
    }
  }
}
```

You can also set `is_rtl`, `skip_entity_detection` and `media`. Rich Markdown supports headings, lists, task lists, tables with alignment, details and footnotes. Its inline styles include `**` or `__` for bold, `*` or `_` for italic, `~~` for strikethrough, `==` for marked text and `||` for spoilers. Math uses `$...$`, `$$...$$` or a fenced `math` block.

Rich HTML supports headings `h1` through `h6`, paragraphs, lists, tables, blockquotes, details, figures and captions. Its ARX tags include `aerx-math`, `aerx-math-block`, `aerx-map`, `aerx-collage`, `aerx-slideshow` and `aerx-document`. Use structured `blocks` for interactive rich buttons. [The rich input models](reference/types/inputrich.md) describe all block fields.

| Rich document limit | Maximum |
| --- | --- |
| Parsed text | 32,768 UTF-16 units |
| Blocks, including nested blocks, list items and table rows | 500 |
| Nesting depth | 16 |
| Media occurrences | 50 |
| Buttons | 100 |
| Table columns | 20 |

An oversized rich document returns HTTP 400 `invalid_parameter` with `reason: rich_limit`. The accepted output contains `rich.blocks` and `rich.is_rtl`. Read the returned tree instead of expecting the original HTML or Markdown.

## Files in rich content <a href="#files-in-rich-content" id="files-in-rich-content"></a>

A structured media block uses `file`, or a `media_id` that refers to an entry in the rich document's `media` array. Choose one source per block.

Set `PHOTO_FILE_ID` to a ready photo returned by [Files](files.md), along with `ARX_BOT_TOKEN`, `CHAT_ID` and a new `REQUEST_KEY`. The SDK file helpers reuse that photo in a rich message:

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, FileInput, RichBlocks, RichMessage } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
const photoFileId = process.env.PHOTO_FILE_ID;
if (!token || !key || !chatId || !photoFileId) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID, PHOTO_FILE_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const result = await bot.messagesSend(chatId, {
		key,
		body: {
			content: new RichMessage([
				RichBlocks.media("photo", FileInput.fromId(photoFileId)),
			]).content(),
		},
	});
	console.log(result);
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models, FileInput, RichBlocks, RichMessage

chat_id = os.environ["CHAT_ID"]
photo_file_id = os.environ["PHOTO_FILE_ID"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.SendMessage(
        content=RichMessage(
            [RichBlocks.media("photo", FileInput.from_id(photo_file_id))]
        ).content(),
    )
    result = bot.messages_send(
        chat_id,
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
    print(result)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{
    Client, Config, FileInput, MediaKind, RichBlocks, RichMediaOptions, RichMessage,
    RichMessageOptions, models,
};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let photo_file_id = std::env::var("PHOTO_FILE_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let photo = RichBlocks::media(
        MediaKind::Photo,
        FileInput::from_id(photo_file_id)?,
        RichMediaOptions::default(),
    )?;
    let rich = RichMessage::new(vec![photo], RichMessageOptions::default())?;
    let request = models::SendMessage::new(rich.content()?);
    let result = bot
        .messages_send(&chat_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

For the dictionary form, each `media` entry requires a unique `id` and a `file` source. For an entry whose `id` is `cover`, reference it from a block with `media_id: cover`, or from markup with `aerx://media?id=cover`. Each use counts toward the media limit, even when you reuse the same file.

`file` is an input source with exactly one of `file_id`, `url` or `attach`. Uploads and URL imports follow the rules in [Files](files.md). [Drafts](drafts.md) accept existing file IDs only. The response contains resolved media instead of input source descriptors.

## Link previews <a href="#link-previews" id="link-previews"></a>

Place `link_preview` beside `content`, not inside the text content. It applies to text messages only. Omitting it lets ARX choose the first eligible URL in the text; URLs covered by spoilers are excluded. Set it to null to suppress the preview, or use the object below.

| Field | Behavior |
| --- | --- |
| `disabled` | Suppresses the preview when true. |
| `url` | Selects an HTTP or HTTPS URL of at most 2,048 bytes, without credentials. |
| `prefer_small_media` | Requests a small image where supported. |
| `prefer_large_media` | Requests a large image where supported. |
| `show_above_text` | Places the preview above the message text. |

Do not set both media preferences to true. On an edit, `link_preview` requires `content`; omitting it preserves preview settings and updates an automatically chosen URL when the text changes. Preview metadata can arrive after the initial message. A failed preview leaves the message text usable.

Continue with [Files](files.md) for captions and attachments, or [Keyboards](keyboards.md) for message actions.
