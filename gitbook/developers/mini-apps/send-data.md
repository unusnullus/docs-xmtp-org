---
description: "Return data from a Mini App with sendData, answer a Mini App query, and share prepared messages."
---

# Send results to your bot

A Mini App can return a result to the conversation in three ways:

| Method | Works with | What happens |
| --- | --- | --- |
| `sendData` | `keyboard_button` launches | The page sends up to 4,096 bytes. Your bot receives them in a service message and the app closes. |
| `mini_app_queries.answer` | Launches with a `query_id`: `menu_button`, `inline_button`, `attach_menu` | Your server sends a message to the chat on behalf of the user who opened the app. |
| `shareMessage` | Any launch | The user shares a message your bot prepared with `prepared_messages.save`. |

## Send data from a reply keyboard button <a href="#send-data-from-a-reply-keyboard-button" id="send-data-from-a-reply-keyboard-button"></a>

Use this when a short form in the app produces one answer for your bot.

### 1. Send the button <a href="#1-send-the-button" id="1-send-the-button"></a>

Send a reply keyboard with a `mini_app` button in a private chat with the user. Set `ARX_BOT_TOKEN`, `CHAT_ID` and `REQUEST_KEY` before running the example.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, ReplyKeyboard } from "@arx/nexo";

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
			content: { type: "text", text: "Tell us about your delivery." },
			reply_markup: new ReplyKeyboard({ resize: true })
				.row({ type: "mini_app", text: "Rate delivery", url: "https://app.example.com/rate" })
				.build(),
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
from arx_nexo import Client, Config, FormattedText, ReplyKeyboard, models

chat_id = os.environ["CHAT_ID"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    button = models.InputReplyButton.model_validate(
        {"type": "mini_app", "text": "Rate delivery", "url": "https://app.example.com/rate"}
    )
    request = models.SendMessage(
        content=FormattedText("Tell us about your delivery.").content(),
        reply_markup=ReplyKeyboard(resize=True).row(button).markup(),
    )
    result = bot.messages_send(chat_id, body=request, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, FormattedText, ReplyKeyboard, ReplyKeyboardOptions, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let button = models::InputReplyButton::InputReplyButtonMiniApp(Box::new(
        models::InputReplyButtonMiniApp::new(
            "Rate delivery".into(),
            "https://app.example.com/rate".into(),
        ),
    ));
    let options = ReplyKeyboardOptions {
        resize: Some(true),
        ..Default::default()
    };
    let keyboard = ReplyKeyboard::new(options).row(vec![button])?;
    let request = models::SendMessage {
        reply_markup: Some(keyboard.markup()?),
        ..models::SendMessage::new(FormattedText::new("Tell us about your delivery.", vec![])?.content()?)
    };
    let result = bot
        .messages_send(&chat_id, &[], Some(&request), Some(&key))
        .await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

### 2. Call sendData in the page <a href="#2-call-senddata-in-the-page" id="2-call-senddata-in-the-page"></a>

`sendData` accepts a string of 1 to 4,096 UTF-8 bytes. Check the size before you enable the button, because multibyte characters count more than once.

```js
const app = window.AERX.WebApp;
await app.whenInitialized();

const rating = document.getElementById("rating");
const comment = document.getElementById("comment");
const canSend = app.initDataUnsafe.launch_kind === "keyboard_button";
const payload = () => JSON.stringify({ rating: Number(rating.value), comment: comment.value });

app.MainButton.setText("Send rating").onClick(async () => {
  app.MainButton.showProgress();
  try {
    await app.sendData(payload());
  } catch (error) {
    app.MainButton.hideProgress();
    app.showAlert(error.code === "NOT_ALLOWED_FOR_LAUNCH_KIND" ? "Open this form from the chat keyboard." : "Try again.");
  }
});

function update() {
  const size = new TextEncoder().encode(payload()).length;
  if (canSend && size <= 4096) app.MainButton.enable().show();
  else app.MainButton.disable();
}
rating.addEventListener("input", update);
comment.addEventListener("input", update);
update();
```

The page can call `sendData` once per launch. Further calls return the same Promise and send nothing. After the data is accepted, ARX closes the app. Outside a `keyboard_button` launch the call rejects with `NOT_ALLOWED_FOR_LAUNCH_KIND`. Do not retry a call whose result you did not receive; ask the user to open the app again.

### 3. Handle the service message <a href="#3-handle-the-service-message" id="3-handle-the-service-message"></a>

Your bot receives a `message` update whose message has `kind` set to `service` and a `service` object:

```json
{
  "type": "mini_app_data",
  "data": "{\"rating\":5,\"comment\":\"On time\"}",
  "button_text": "Rate delivery"
}
```

`button_text` is the label of the button that opened the app. `data` comes from the page without a signature. Parse and validate it like any user input. The sender of the message is the user.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

function readRating(data: string): number | undefined {
	try {
		const value = (JSON.parse(data) as { rating?: unknown }).rating;
		return Number.isInteger(value) && (value as number) >= 1 && (value as number) <= 5
			? (value as number)
			: undefined;
	} catch {
		return undefined;
	}
}

const bot = new Client({ baseUrl: process.env.ARX_BOT_API_BASE_URL });
let offset: string | undefined;
try {
	while (true) {
		const batch = await bot.updatesGet({
			query: { timeout_seconds: 30, ...(offset ? { offset } : {}) },
		});
		for (const update of batch.updates) {
			const message = update.type === "message" ? update.message : undefined;
			if (message?.kind === "service") {
				const service = message.service;
				if (service.type === "mini_app_data") {
					const rating = readRating(service.data);
					if (rating !== undefined) {
						await bot.sendText(message.chat.id, `Thanks for rating us ${rating}/5.`, {
							key: `rating-${update.update_id}`,
						});
					}
				}
			}
			offset = (BigInt(update.update_id) + 1n).toString();
		}
	}
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import json
import os
from arx_nexo import Client, Config

with Client(Config(base_url=os.environ["ARX_BOT_API_BASE_URL"])) as bot:
    offset: str | None = None
    while True:
        batch = bot.updates_get(offset=offset, timeout_seconds=30)
        for update in batch.updates:
            raw = update.model_dump(mode="json")
            message = raw.get("message") if update.type == "message" else None
            service = message.get("service") if message and message.get("kind") == "service" else None
            if service and service.get("type") == "mini_app_data":
                try:
                    rating = json.loads(service["data"]).get("rating")
                except (ValueError, AttributeError):
                    rating = None
                if type(rating) is int and 1 <= rating <= 5:
                    bot.send_text(
                        message["chat"]["id"],
                        f"Thanks for rating us {rating}/5.",
                        key=f"rating-{update.update_id}",
                    )
            offset = str(int(update.update_id) + 1)
```

{% endtab %}
{% tab title="Rust" %}

Add `serde_json` and `tokio` with the `macros` and `rt-multi-thread` features to your dependencies.

```rust
use arx_nexo::{Client, Config, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let config = Config::new(
        std::env::var("ARX_BOT_TOKEN")?,
        &std::env::var("ARX_BOT_API_BASE_URL")?,
        false,
    )?;
    let bot = Client::new(config)?;
    let mut offset: Option<String> = None;
    loop {
        let batch = {
            let mut query = vec![("timeout_seconds", "30")];
            if let Some(value) = offset.as_deref() {
                query.push(("offset", value));
            }
            bot.updates_get(&query, None, None).await?
        };
        for update in batch.updates {
            if let Some(models::IncomingMessage::Message(message)) = update.message() {
                if let models::Message::ServiceMessage(service) = message.as_ref() {
                    if let models::Service::MiniAppDataService(data) = &service.service {
                        let value: serde_json::Value =
                            serde_json::from_str(&data.data).unwrap_or(serde_json::Value::Null);
                        if let Some(rating) = value["rating"].as_u64().filter(|r| (1..=5).contains(r)) {
                            let key = format!("rating-{}", update.update_id());
                            let text = format!("Thanks for rating us {rating}/5.");
                            bot.send_text(&service.chat.id, &text, Some(&key)).await?;
                        }
                    }
                }
            }
            let next = update.update_id().parse::<u64>()?
                .checked_add(1).ok_or("Update ID overflow")?;
            offset = Some(next.to_string());
        }
    }
}
```

{% endtab %}
{% endtabs %}

## Answer a Mini App query <a href="#answer-a-mini-app-query" id="answer-a-mini-app-query"></a>

When the app opens from the menu button, an inline button or the attachment menu, its launch data contains a `query_id`. Your server can use it once to send a message to the chat where the user opened the app. The message appears as sent by the user through your bot.

1. The page sends `initData` and the user's choice to your server.
2. Your server [validates the launch data](validate-launch-data.md) and reads `query_id`.
3. Your server calls `mini_app_queries.answer` with one inline result. Reuse the same `Idempotency-Key` if you retry.
4. The page closes itself, or shows a confirmation.

The request body has one `result`, in the same format as an inline query result. This example sends an article with text. Set `ARX_BOT_TOKEN`, `QUERY_ID` and `REQUEST_KEY` before running it.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const queryId = process.env.QUERY_ID;
if (!token || !key || !queryId) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, QUERY_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const result = await bot.miniAppQueriesAnswer(queryId, {
		key,
		body: {
			result: {
				type: "article",
				id: "order-42",
				title: "Order 42",
				content: { type: "text", text: "I ordered 2 coffees for pickup at 9:30." },
			},
		},
	});
	console.log(result.delivery);
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models

with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.AnswerMiniAppQuery.model_validate(
        {
            "result": {
                "type": "article",
                "id": "order-42",
                "title": "Order 42",
                "content": {"type": "text", "text": "I ordered 2 coffees for pickup at 9:30."},
            }
        }
    )
    result = bot.mini_app_queries_answer(
        os.environ["QUERY_ID"],
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
    print(result.delivery)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let query_id = std::env::var("QUERY_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let content = models::InlineMessageContent::InlineText(Box::new(models::InlineText::new(
        "I ordered 2 coffees for pickup at 9:30.".into(),
    )));
    let article = models::InlineResultArticle::new(content, "order-42".into(), "Order 42".into());
    let request = models::AnswerMiniAppQuery::new(models::InlineResult::InlineResultArticle(
        Box::new(article),
    ));
    let result = bot
        .mini_app_queries_answer(&query_id, &[], Some(&request), Some(&key))
        .await?;
    println!("{:?}", result.delivery);
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The response contains `delivery`: `sent` when ARX sent the message, or `client_mediated` when the user's app delivers it. It can also contain an `inline_message_id` for later operations on that message.

| Error | Meaning |
| --- | --- |
| 404 `query_not_found` | The query ID is unknown or belongs to another bot. |
| 400 `unsupported_content` | This launch has no chat to answer in. |
| 409 `query_already_answered` | The query already has an answer. Replaying the original key and body returns the original result. |
| 410 `query_expired` | The Mini App session has ended. |
| 429 `mini_app_query_too_early` | You answered before `can_send_after`. Wait and retry with the same key. |
| 403 `capability_not_available` | Mini Apps are not available for this bot, or the result is a game or sticker, shares a live location or has a callback button. |

The typed methods are `miniAppQueriesAnswer` in TypeScript and `mini_app_queries_answer` in Python and Rust. See the [`mini_app_queries.answer` reference](../reference/mini-apps.md#operation-mini-app-queries-answer).

## Share a prepared message <a href="#share-a-prepared-message" id="share-a-prepared-message"></a>

Your server can save a message for one user, and the page lets that user share it to a chat they choose.

1. Your server calls [`prepared_messages.save`](../reference/mini-apps.md#operation-prepared-messages-save) with the user's ID from verified launch data, one inline result, and the destinations you allow: `allow_user_chats`, `allow_bot_chats`, `allow_group_chats` or `allow_channel_chats`. The saved message expires after 24 hours.
2. Your server returns the saved `id` to the page.
3. The page calls `shareMessage(id)` from a tap. ARX emits `shareMessageSent` when the user sends it, or `shareMessageFailed` with an `error`.

```json
{
  "user_id": "700001234",
  "allow_user_chats": true,
  "allow_group_chats": true,
  "result": {
    "type": "article",
    "id": "invite",
    "title": "Join my team",
    "content": { "type": "text", "text": "Join my team in Acme Tasks." }
  }
}
```

```js
app.onEvent("shareMessageSent", () => app.showAlert("Invitation sent."));
shareButton.addEventListener("click", () => {
  app.shareMessage(preparedId).catch(() => {});
});
```

[`prepared_buttons.save`](../reference/mini-apps.md#operation-prepared-buttons-save) works the same way for a user picker: it saves a `request_users` button for one user, and the page opens it with `requestChat(id)`. Your bot receives the selection as a `users_shared` service message. A bot can keep up to 100 active prepared buttons per user.
