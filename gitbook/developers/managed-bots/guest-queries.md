---
description: "Answer a single message in a group or encrypted chat where your bot is not a member."
---

# Guest queries

With **Guest queries**, people can ask your bot something in a chat it has not joined. They mention your bot, your bot receives only that message, and it posts one answer into the chat. Your bot does not become a member and receives nothing else from the chat.

Turn on **Guest queries** in [Advanced modes](../managed-bots.md) first.

## What people do <a href="#what-people-do" id="what-people-do"></a>

In a group, a person types `@` and picks your bot. Bots available as guests show a **Guest** label in the suggestions. Before sending, ARX tells the person what your bot will receive:

- Only this message, or
- This message and the message they reply to, when they reply to a message.

After sending, the person can see each guest bot's status: **Queued**, **Waiting for a reply** or **Answered**. Your answer appears in the chat attributed to your bot and to the person who asked. Members can report a guest bot.

A message can invite at most three guest bots. A bot is invited as a guest only when all of these hold:

- Its owner turned on Guest queries.
- It is not already a member of the chat. Members receive messages through the normal updates instead.
- The person who asks is allowed to use it.
- The chat allows guest bots. Groups allow them unless their settings turn guest bots off. Channels never do.

Guest queries also work in end-to-end encrypted chats. In that case the person's app discloses the message text to your bot, and ARX cannot verify it.

## What your bot receives <a href="#what-your-bot-receives" id="what-your-bot-receives"></a>

Your bot receives a `guest_message` update. `guest_message` has one of two shapes.

| Shape | How to recognize it | What it contains |
| --- | --- | --- |
| Group message | `chat.kind` is a group kind | The accepted message: `message_id`, `chat`, `content`, `fallback_text`, `sent_at`, the author in `sender_user` or `sender_chat`, and at most the message it replies to in `reply_to`. `guest_caller_user` or `guest_caller_chat` repeats the public author. |
| Encrypted chat | `chat.kind` is `e2e_guest` | `message.text` and `message.entities` disclosed by the person, `guest_caller_user`, and `client_asserted` with `chat_type` (`e2e_private` or `e2e_group`) and an optional quoted `reply_to`. There is no `message_id`. |

Both shapes include `guest_query_id`, a 32-character hexadecimal ID, and `answer_deadline_at`. `is_from_offline` is always `false` for guest messages; you can ignore it.

Treat the content as input from one person, not as the chat:

- You receive no history, member list or chat title, and no later messages.
- When someone posts anonymously as the group or a channel, you get that public chat identity in `guest_caller_chat`, never the person behind it.
- In encrypted chats, `chat.id` is a pseudonymous ID. It differs for each person who asks, so do not use it to link people.
- In encrypted chats, `message` and `client_asserted` are claims by the person's app. A quote with `author` `bot` is not proof that your bot wrote it. Encrypted guest messages carry text only, no attachments.

## Answer the query <a href="#answer-the-query" id="answer-the-query"></a>

Call [`guest_queries.answer`](../reference/mini-apps.md#operation-guest-queries-answer) with the `guest_query_id` and one inline result. Use `bot.guestQueries.answer` in TypeScript, `bot.guest_queries.answer` in Python and `bot.guest_queries().answer` in Rust. The response contains `inline_message_id`, a reference to your posted answer. It confirms that ARX accepted the answer, not that every device has shown it.

The result's content can be text, rich text, a location, a venue or a contact. An `article` result with text content is the most common choice. Results that need a file, such as photos or documents, return `capability_not_available` and leave the query open, so you can answer again with supported content.

Each query takes one answer. Answer before `answer_deadline_at`; the default window is 60 seconds. The SDK methods require a `key` argument. Pass any stable value, such as `guest-<guest_query_id>`; the server ignores it. If a response is lost and your retry returns `query_already_answered`, the first answer was accepted.

## Complete example <a href="#complete-example" id="complete-example"></a>

This bot answers every guest query with an article that repeats the question. Set `ARX_BOT_TOKEN` before running it.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, type Models, Polling } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
if (!token) throw new Error("Set ARX_BOT_TOKEN");
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
const polling = new Polling(bot);

function isEncrypted(guest: Models.GuestMessage): guest is Models.E2eGuestMessage {
	return guest.chat.kind === "e2e_guest";
}

function question(guest: Models.GuestMessage): string {
	return isEncrypted(guest) ? guest.message.text : (guest.fallback_text ?? "");
}

try {
	for (;;) {
		await polling.step(async (update) => {
			const guest = update.type === "guest_message" ? update.guest_message : undefined;
			if (!guest) return;
			const result: Models.InlineResult = {
				type: "article",
				id: "answer",
				title: "Answer",
				content: { type: "text", text: `You asked: ${question(guest).slice(0, 200)}` },
			};
			const sent = await bot.guestQueries.answer(guest.guest_query_id, result, {
				key: `guest-${guest.guest_query_id}`,
			});
			console.log("answered", sent.inline_message_id);
		});
	}
} finally {
	try {
		await polling.close();
	} finally {
		await bot.close();
	}
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os

from arx_nexo import Client, Config, Update, models
from arx_nexo.polling import Polling

with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:

    def handle(update: Update) -> None:
        if update.type != "guest_message" or update.guest_message is None:
            return
        guest = update.guest_message.root
        if isinstance(guest, models.E2eGuestMessage):
            asked = guest.message.text
        else:
            asked = guest.fallback_text or ""
        result = models.InlineResult.model_validate(
            {
                "type": "article",
                "id": "answer",
                "title": "Answer",
                "content": {"type": "text", "text": f"You asked: {asked[:200]}"},
            }
        )
        sent = bot.guest_queries.answer(
            guest.guest_query_id, result, key=f"guest-{guest.guest_query_id}"
        )
        print("answered", sent.inline_message_id)

    polling = Polling(bot)
    try:
        while True:
            polling.step(handle)
    finally:
        polling.close()
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, models, polling::Polling};
use serde_json::json;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let client = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let mut polling = Polling::new(client.clone());
    let bot = &client;
    loop {
        let step = polling
            .step(
                |update| async move {
                    let Some(guest) = update.guest_message() else {
                        return Ok::<(), arx_nexo::Error>(());
                    };
                    let (query_id, asked) = match guest {
                        models::GuestMessage::NexoGuestMessage(message) => (
                            message.guest_query_id.clone(),
                            message.fallback_text.clone().unwrap_or_default(),
                        ),
                        models::GuestMessage::E2eGuestMessage(message) => {
                            (message.guest_query_id.clone(), message.message.text.clone())
                        }
                    };
                    let text: String = asked.chars().take(200).collect();
                    let result: models::InlineResult = serde_json::from_value(json!({
                        "type": "article",
                        "id": "answer",
                        "title": "Answer",
                        "content": {"type": "text", "text": format!("You asked: {text}")}
                    }))
                    .map_err(|_| arx_nexo::Error::Decode)?;
                    let sent = bot
                        .guest_queries()
                        .answer(&query_id, &result, &format!("guest-{query_id}"))
                        .await?;
                    println!("answered {}", sent.inline_message_id);
                    Ok(())
                },
                30,
            )
            .await;
        if let Err(error) = step {
            polling.close().await?;
            return Err(error.into());
        }
    }
}
```

{% endtab %}
{% endtabs %}

## Errors <a href="#errors" id="errors"></a>

| Code | HTTP | Meaning |
| --- | --- | --- |
| `query_already_answered` | 409 | The query already has an answer. |
| `query_expired` | 410 | The answer window closed. |
| `query_not_found` | 404 | The ID is unknown or belongs to another bot. |
| `capability_not_available` | 403 | The content type is not supported for guest answers, or Guest queries is off for your bot. The query stays open for supported content. |
| `result_invalid` | 400 | The inline result does not match its schema. |

Do not retry `query_already_answered`, `query_expired` or `query_not_found`. See [Errors](../errors.md) for the common error format.

## Related pages <a href="#related-pages" id="related-pages"></a>

- [Advanced modes](../managed-bots.md) explains how to turn the mode on.
- [Formatting](../formatting.md) covers text entities and rich text.
- [Polling and webhooks](../updates.md) covers update filters.
