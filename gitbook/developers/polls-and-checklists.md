---
description: "Collect votes, create quizzes and track tasks in a private chat."
---

# Polls and checklists

You can send a poll to collect votes or a checklist to track work. Use `messages.send` in a private chat, keep the IDs in its response, and read subsequent updates to follow the result.

## Send a poll <a href="#send-a-poll" id="send-a-poll"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID` and `REQUEST_KEY` before running the example. Use a private chat that the user has started. See [Making requests](making-requests.md) for choosing keys and retrying requests.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

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
			content: {
				type: "poll",
				poll: {
					question: "Which meeting time works?",
					options: [{ text: "Morning" }, { text: "Afternoon" }],
					is_anonymous: false,
					allows_multiple_answers: true,
				},
			},
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
from arx_nexo import Client, Config, models

chat_id = os.environ["CHAT_ID"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.SendMessage(
        content=models.InputContent(
            root=models.InputPollContent(
                type="poll",
                poll=models.InputPoll(
                    question="Which meeting time works?",
                    options=[
                        models.InputPollOption(text="Morning"),
                        models.InputPollOption(text="Afternoon"),
                    ],
                    is_anonymous=False,
                    allows_multiple_answers=True,
                ),
            )
        ),
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
use arx_nexo::{Client, Config, models, presence::Presence};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let request = models::SendMessage {
        content: models::InputContent::InputPollContent(Box::new(models::InputPollContent {
            r#type: models::InputPollContentType::Poll,
            poll: models::InputPoll {
                allow_adding_options: None,
                allows_multiple_answers: Some(true),
                allows_revoting: None,
                close_at: None,
                correct_option_ids: None,
                country_codes: None,
                description: None,
                description_entities: None,
                description_format: None,
                explanation: None,
                explanation_entities: None,
                explanation_format: None,
                explanation_media: None,
                hide_results_until_closes: None,
                is_anonymous: Some(false),
                is_closed: None,
                media: None,
                members_only: None,
                open_period_seconds: None,
                options: vec![
                    models::InputPollOption {
                        media: None,
                        text: "Morning".into(),
                        text_entities: None,
                    },
                    models::InputPollOption {
                        media: None,
                        text: "Afternoon".into(),
                        text_entities: None,
                    },
                ],
                question: "Which meeting time works?".into(),
                question_entities: None,
                shuffle_options: None,
                r#type: None,
            },
        })),
        effect_id: None,
        link_preview: Presence::Unset,
        protect_content: None,
        reply_markup: None,
        reply_to: None,
        silent: None,
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

Input options have no IDs. Read the accepted `content.poll.options[].id` values from the returned content message. These UUIDs identify options even when their display order changes. The `poll.id` identifies the poll itself.

| Poll field | Rule |
| --- | --- |
| `question` | Required. Use 1 to 300 UTF-16 units. |
| `options` | Required. Provide 1 to 12 options, each with `text` of 1 to 100 UTF-16 units. |
| `type` | Defaults to `regular`; use `quiz` for a quiz. |
| `is_anonymous` | Defaults to true. |
| `allows_multiple_answers` | Defaults to false. |
| `allows_revoting` | Defaults to true for regular polls and false for quizzes. A quiz rejects true. |
| `description` | Optional descriptive text. See the shared [formatting fields and limits](formatting.md#choose-the-formatting-fields). |
| `explanation` | Quiz-only text shown with the answer. It follows the shared formatting rules. |
| `open_period_seconds` or `close_at` | Choose one. Set a period from 5 to 2,628,000 seconds (about 30 days). An RFC 3339 `close_at` must be at least 5 seconds and at most 2,628,000 seconds in the future. |
| `shuffle_options` | Defaults to false. Shuffles the display order. |
| `hide_results_until_closes` | Defaults to false. Hides results until closure. |
| `allow_adding_options` | Defaults to false. Requires a regular, non-anonymous poll. |
| `is_closed` | Defaults to false. You can send a poll that is already closed. |
| `members_only` | Omit it or use false for a private chat. |

Optional poll fields reject null. Questions and options use plain text. See [Not available yet](not-available-yet.md) for restricted poll features.

## Create a quiz <a href="#create-a-quiz" id="create-a-quiz"></a>

Set `type` to `quiz` and provide `correct_option_ids`. These are zero-based input indexes, not option UUIDs. Provide unique indexes in strictly increasing order and within the input options. ARX maps them to persistent option IDs when it accepts the message. For HTTP:

```json
{
  "content": {
    "type": "poll",
    "poll": {
      "type": "quiz",
      "question": "How many seconds are in a minute?",
      "options": [{"text":"30"},{"text":"60"},{"text":"90"}],
      "correct_option_ids": [1],
      "explanation": "A minute contains sixty seconds."
    }
  }
}
```

A quiz allows one attempt. Multiple correct choices require `allows_multiple_answers: true`; otherwise provide exactly one correct index. Regular polls reject answer indexes, explanations and explanation media. The Poll returned to your bot includes correct answers when the poll is closed, your bot created it, or the poll belongs to a private chat. Users see the answers and explanation after they vote or the poll closes.

## Add poll media <a href="#add-poll-media" id="add-poll-media"></a>

The media fields are `poll.media`, `poll.explanation_media` and `poll.options[].media`. Explanation media belongs to a quiz. Each field takes one typed media object.

| Media position | Accepted types |
| --- | --- |
| Poll or quiz explanation | `photo`, `video`, `animation`, `audio`, `document`, `live_photo`, static `location`, `venue` |
| Option | `photo`, `video`, `animation`, `live_photo`, static `location`, `venue`, `link` |

For a photo, use an `InputPhoto` with its `photo` source. For a document, use an `InputDocument` with its `document` source. A link option uses the `link` type with a `url`, such as `https://example.com/details`. These are media objects, not `file` fields on the poll or option.

File sources and upload restrictions follow [Files](files.md). [Locations and contacts](locations-and-contacts.md) covers static place fields. Poll locations cannot contain `live`. The response returns resolved media, without upload descriptors or source URLs.

## Close a poll and change its options <a href="#close-a-poll-and-change-its-options" id="close-a-poll-and-change-its-options"></a>

Call `messages.stop_poll` with the chat and message IDs and body `{}` to close your bot's open poll. The response contains the final Poll, and your bot receives a final `poll` update. Omit `reply_markup` to keep its keyboard, or supply an inline replacement using the [keyboard edit rules](keyboards.md#edit-or-remove-inline-buttons).

Other authors' polls return `access_restricted`; already closed polls return HTTP 409 `poll_closed`. Retry an uncertain close with the original key and body to recover the result. `messages.edit` can change the keyboard but cannot replace poll content.

When `allow_adding_options` is true, users can add text options up to the same 12-option total. Your bot can remove a user-added option by UUID with `messages.delete_poll_option`; it cannot remove an original option. Additions and deletions arrive as `poll_option_added` or `poll_option_deleted` service messages. Removing an option adjusts selections and counts without creating an individual vote update.

## Send a checklist <a href="#send-a-checklist" id="send-a-checklist"></a>

Send a checklist with stable task IDs that you choose. Set a fresh `REQUEST_KEY` for this send.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

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
			content: {
				type: "checklist",
				checklist: {
					title: "Prepare the meeting",
					tasks: [
						{ id: "agenda", text: "Send the agenda" },
						{ id: "room", text: "Reserve the room" },
					],
					others_can_add_tasks: true,
					others_can_mark_tasks_as_done: true,
				},
			},
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
from arx_nexo import Client, Config, models

chat_id = os.environ["CHAT_ID"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.SendMessage(
        content=models.InputContent(
            root=models.InputChecklistContent(
                type="checklist",
                checklist=models.InputChecklist(
                    title="Prepare the meeting",
                    tasks=[
                        models.InputChecklistTask(id="agenda", text="Send the agenda"),
                        models.InputChecklistTask(id="room", text="Reserve the room"),
                    ],
                    others_can_add_tasks=True,
                    others_can_mark_tasks_as_done=True,
                ),
            )
        ),
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
use arx_nexo::{Client, Config, models, presence::Presence};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let request = models::SendMessage {
        content: models::InputContent::InputChecklistContent(Box::new(
            models::InputChecklistContent {
                r#type: models::InputChecklistContentType::Checklist,
                checklist: models::InputChecklist {
                    others_can_add_tasks: Some(true),
                    others_can_mark_tasks_as_done: Some(true),
                    tasks: vec![
                        models::InputChecklistTask {
                            id: "agenda".into(),
                            text: "Send the agenda".into(),
                            text_entities: None,
                            text_format: None,
                        },
                        models::InputChecklistTask {
                            id: "room".into(),
                            text: "Reserve the room".into(),
                            text_entities: None,
                            text_format: None,
                        },
                    ],
                    title: "Prepare the meeting".into(),
                    title_entities: None,
                    title_format: None,
                },
            },
        )),
        effect_id: None,
        link_preview: Presence::Unset,
        protect_content: None,
        reply_markup: None,
        reply_to: None,
        silent: None,
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

A checklist requires `title` and 1 to 30 tasks. Every task requires `id` and `text`. IDs are unique and case sensitive, with 1 to 64 ASCII letters, digits, underscores or hyphens. Text limits and supported styles live in [Formatting](formatting.md#choose-the-formatting-fields).

`others_can_add_tasks` and `others_can_mark_tasks_as_done` both default to false. The author can always perform these actions; other users need the corresponding permission. Your bot can create or edit a checklist, but task completion and task addition are user actions without Bot API endpoints. Do not submit completion, author or position fields.

## Checklist state and edits <a href="#checklist-state-and-edits" id="checklist-state-and-edits"></a>

The accepted `Checklist` contains formatted text. Completed tasks include `completed_by_user` and `completed_at`. A user's task action changes `state_version`; it does not change the message's `revision`. Repeating completion preserves the original completing user and time.

Your bot receives `checklist_tasks_added` and `checklist_tasks_done` as service messages inside ordinary `message` updates. As the author, you can replace a checklist through `messages.edit`. Reuse task IDs to preserve completion, and omit a task ID to remove that task. Copies and forwards have independent task state.

## Receive votes and results <a href="#receive-votes-and-results" id="receive-votes-and-results"></a>

| Update | What it tells you |
| --- | --- |
| `poll` | Aggregate state, at most once per five seconds for each poll. Closure sends the final state immediately. |
| `poll_answer` | A non-anonymous voter's `user`, `poll_id` and persistent `option_ids`. An empty selection withdraws a vote when revoting is allowed. |

Read [Updates](updates.md) for delivery and filtering. A poll aggregate does not identify individual voters.

Copying or forwarding a poll creates new poll and option IDs, remaps the correct answers and resets votes. It keeps configuration, media, the absolute closing deadline and the closed state. Keep the new IDs separately from the source poll.

## Reply to an option or task <a href="#reply-to-an-option-or-task" id="reply-to-an-option-or-task"></a>

Pair `reply_to.message_id` with either `poll_option_id` or `checklist_task_id`. This HTTP body uses sample IDs from an accepted poll:

```json
{
  "content": {"type":"text","text":"I'll reserve this time."},
  "reply_to": {"message_id":"12","poll_option_id":"f43b0c0a-744c-42e9-9bd9-1fcf7bb88fe2"}
}
```

The selected part must exist when ARX accepts the request. New messages, albums and copies support these selectors. Forwarding does not accept `reply_to`.

| Invalid input | Error |
| --- | --- |
| Quote text, formatting or UTF-16 position does not match | HTTP 400 `quote_not_found` |
| Invalid poll option | HTTP 400 `poll_option_invalid` |
| Invalid checklist task | HTTP 400 `request_not_valid` |
| Both part selectors | HTTP 400 `invalid_request` |

A reply stores a snapshot of the original message as it was when ARX accepted the reply. Quiz reply context hides answers and explanations, so read the authorized original poll when you need them.

Continue with [Keyboards](keyboards.md) to request a user-created poll, or [the interactive reference](reference/interactive.md) for the poll operations.
