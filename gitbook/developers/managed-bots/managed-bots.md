---
description: "Create bots for people who ask your bot for one, then read and rotate their tokens and control who can use them."
---

# Managed bots

With the **Manage bots** mode, your bot becomes a manager. A person asks your bot to create a bot for them, confirms the name and handle in ARX, and becomes its owner. Your bot can then read the new bot's token, run the bot, rotate its token and limit who can use it.

The person always stays the owner. They can unlink your bot or transfer their bot at any time.

Turn on **Manage bots** in [Advanced modes](../managed-bots.md) first.

## How creation works <a href="#how-creation-works" id="how-creation-works"></a>

1. Your bot offers a creation button or link to the person in its private chat.
2. The person taps it. ARX opens a sheet titled **Create managed bot** with your bot's name.
3. The person edits the suggested name and handle, checks that the handle is free, and accepts the disclosure.
4. ARX creates the bot. The person sees that the bot is ready and that your bot manages it.
5. Your bot receives a `managed_bot` update and a `managed_bot_created` service message in the private chat.
6. Your bot reads the new bot's token with `managed_bots.get_token`.

The disclosure tells the person that your bot will have full control of the new bot: messages, profile, settings and chats. They must check **Allow this manager to control this bot** before creating it. If they have not accepted the developer terms, ARX shows them first.

The handle must have 5 to 21 uppercase letters or digits followed by `BOT`, for example `ACMESUPPORTBOT`. If your suggested handle has 21 characters or fewer and does not end in `BOT`, ARX adds it. Suggest an uppercase handle; the person must correct any other suggestion before creating the bot.

## Offer a creation button <a href="#offer-a-creation-button" id="offer-a-creation-button"></a>

Send a reply keyboard with a `request_managed_bot` button in a private chat with the person.

| Field | Rules |
| --- | --- |
| `type` | `request_managed_bot`. |
| `text` | The button label, 1 to 64 UTF-16 code units. |
| `request_id` | 1 to 64 ASCII letters, digits, underscores or hyphens. It must be unique within the keyboard. |
| `suggested_name` | Optional. 1 to 64 UTF-16 code units. The person can edit it. |
| `suggested_username` | Optional. 1 to 64 ASCII letters or digits. The person can edit it. It does not reserve the handle. |
| `style` | Optional. `primary`, `success` or `danger`. |

The button does not grant creation by itself. ARX verifies the request when the person confirms.

A Mini App of your bot can offer the same button through [`prepared_buttons.save`](../reference/mini-apps.md#operation-prepared-buttons-save). A prepared button is bound to one person, lasts 24 hours, and a bot can keep at most 100 active prepared buttons per person. Mini Apps must be enabled for your bot.

## Offer a creation link <a href="#offer-a-creation-link" id="offer-a-creation-link"></a>

You can also share a link:

```text
https://www.arx.pro/newbot/<manager handle>/<suggested handle>?name=<suggested name>
```

Use your bot's handle without `@` as the manager handle. The suggested handle and the `name` parameter are optional. For example, `https://www.arx.pro/newbot/ACMEBUILDERBOT/ACMESUPPORT?name=Acme%20Support` opens the creation sheet with Acme Support and `ACMESUPPORTBOT`. If the link is no longer valid, ARX tells the person to ask your bot for a new one.

## What your bot receives <a href="#what-your-bot-receives" id="what-your-bot-receives"></a>

A `managed_bot` update contains `user` (the owner), `bot` (the managed bot) and `is_managed`.

| Event | `is_managed` |
| --- | --- |
| The person created a bot with your bot as manager. | `true` |
| Your bot rotated the managed bot's token. | `true` |
| The owner unlinked your bot. | `false` |
| The owner transferred the bot to another account. | `false` |

When `is_managed` is `false`, your bot no longer manages that bot. Delete the token you stored for it. The managed-bot methods return `managed_bot_detached` for it from then on.

The `managed_bot_created` service message in your private chat with the person contains `bot`, the new bot. It confirms creation in the conversation; use the `managed_bot` update for your records.

## Manage tokens and access <a href="#manage-tokens-and-access" id="manage-tokens-and-access"></a>

| Operation | TypeScript | Python | Rust | What it does |
| --- | --- | --- | --- | --- |
| [`managed_bots.get_token`](../reference/managed-bots.md#operation-managed-bots-get-token) | `bot.managedBots.getToken` | `bot.managed_bots.get_token` | `bot.managed_bots().get_token` | Reads the current token. |
| [`managed_bots.rotate_token`](../reference/managed-bots.md#operation-managed-bots-rotate-token) | `bot.managedBots.rotateToken` | `bot.managed_bots.rotate_token` | `bot.managed_bots().rotate_token` | Replaces the token and revokes the old one at once. |
| [`managed_bots.get_access`](../reference/managed-bots.md#operation-managed-bots-get-access) | `bot.managedBots.getAccess` | `bot.managed_bots.get_access` | `bot.managed_bots().get_access` | Reads who can use the bot. |
| [`managed_bots.set_access`](../reference/managed-bots.md#operation-managed-bots-set-access) | `bot.managedBots.setAccess` | `bot.managed_bots.set_access` | `bot.managed_bots().set_access` | Replaces who can use the bot. |

Every call uses your manager bot's own token. The SDK returns the managed bot's token in a wrapper that hides it from logs and serialization: call `reveal()` in TypeScript, `token.get_secret_value()` in Python or `token.expose_secret()` in Rust when you hand it to a new client or a secret store.

Token rules:

- Each read is recorded. Read the token when you need it and do not cache the response.
- After a rotation the previous token stops working immediately. Your bot receives a `managed_bot` update and the owner gets a notice.
- Send an idempotency key with `rotate_token`. If the result is uncertain, retry with the same key to get the original rotation instead of rotating again.
- `token_not_issued` means the bot has no current token.

Access rules:

- `is_access_restricted: true` limits who can start the bot to the owner and up to 10 people in `added_user_ids`. Send distinct decimal user IDs.
- `is_access_restricted: false` lets anyone use the bot. The `added_user_ids` you send are ignored and the saved list is kept, but the list may still hold at most 10 entries.
- The owner always has access and is never listed. `get_access` returns the listed people as `added_users`.
- The owner can change the same setting in ARX under **Who can use this bot**. People outside the list see that the owner limited the bot.
- Send an idempotency key with `set_access`.

## Complete example <a href="#complete-example" id="complete-example"></a>

This manager bot sends a creation button to one person, then stores each new bot's token in a private directory and removes it when the bot is unlinked or transferred.

Set `ARX_BOT_TOKEN`, `CHAT_ID` (a private chat with the person), `REQUEST_KEY` and `TOKEN_DIR` (an existing directory with mode `0700`) before running it.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { rm, writeFile } from "node:fs/promises";
import { join } from "node:path";
import { Client, Polling } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const key = process.env.REQUEST_KEY;
const tokenDir = process.env.TOKEN_DIR;
if (!token || !chatId || !key || !tokenDir) {
	throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, TOKEN_DIR");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
const polling = new Polling(bot);
try {
	await bot.messagesSend(chatId, {
		key,
		body: {
			content: { type: "text", text: "Tap the button to create your support bot." },
			reply_markup: {
				type: "reply_keyboard",
				resize: true,
				one_time: true,
				rows: [
					[
						{
							type: "request_managed_bot",
							text: "Create my bot",
							request_id: "support-bot",
							suggested_name: "Acme Support",
							suggested_username: "ACMESUPPORT",
						},
					],
				],
			},
		},
	});
	for (;;) {
		await polling.step(async (update) => {
			const event = update.type === "managed_bot" ? update.managed_bot : undefined;
			if (!event) return;
			const path = join(tokenDir, `${event.bot.id}.token`);
			if (!event.is_managed) {
				await rm(path, { force: true });
				console.log("released", event.bot.id);
				return;
			}
			const managed = await bot.managedBots.getToken(event.bot.id);
			await writeFile(path, managed.reveal(), { mode: 0o600 });
			console.log("managing", event.bot.id, "for", event.user.id);
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
from pathlib import Path

from arx_nexo import Client, Config, Update, models
from arx_nexo.polling import Polling

token_dir = Path(os.environ["TOKEN_DIR"])

with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:

    def handle(update: Update) -> None:
        event = update.managed_bot if update.type == "managed_bot" else None
        if event is None:
            return
        path = token_dir / f"{event.bot.id}.token"
        if not event.is_managed:
            path.unlink(missing_ok=True)
            print("released", event.bot.id)
            return
        managed = bot.managed_bots.get_token(event.bot.id)
        descriptor = os.open(path, os.O_WRONLY | os.O_CREAT | os.O_TRUNC, 0o600)
        with os.fdopen(descriptor, "w") as output:
            output.write(managed.token.get_secret_value())
        print("managing", event.bot.id, "for", event.user.id)

    request = models.SendMessage.model_validate(
        {
            "content": {"type": "text", "text": "Tap the button to create your support bot."},
            "reply_markup": {
                "type": "reply_keyboard",
                "resize": True,
                "one_time": True,
                "rows": [
                    [
                        {
                            "type": "request_managed_bot",
                            "text": "Create my bot",
                            "request_id": "support-bot",
                            "suggested_name": "Acme Support",
                            "suggested_username": "ACMESUPPORT",
                        }
                    ]
                ],
            },
        }
    )
    bot.messages_send(os.environ["CHAT_ID"], body=request, key=os.environ["REQUEST_KEY"])
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
use std::{io::Write, os::unix::fs::OpenOptionsExt, path::PathBuf};

use arx_nexo::{Client, Config, models, polling::Polling};
use serde_json::json;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let key = std::env::var("REQUEST_KEY")?;
    let token_dir = PathBuf::from(std::env::var("TOKEN_DIR")?);
    let client = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let request: models::SendMessage = serde_json::from_value(json!({
        "content": {"type": "text", "text": "Tap the button to create your support bot."},
        "reply_markup": {
            "type": "reply_keyboard",
            "resize": true,
            "one_time": true,
            "rows": [[{
                "type": "request_managed_bot",
                "text": "Create my bot",
                "request_id": "support-bot",
                "suggested_name": "Acme Support",
                "suggested_username": "ACMESUPPORT"
            }]]
        }
    }))?;
    client
        .messages_send(&chat_id, &[], Some(&request), Some(&key))
        .await?;
    let mut polling = Polling::new(client.clone());
    let bot = &client;
    let token_dir = &token_dir;
    loop {
        let step = polling
            .step(
                |update| async move {
                    let Some(event) = update.managed_bot() else {
                        return Ok::<(), std::io::Error>(());
                    };
                    let path = token_dir.join(format!("{}.token", event.bot.id));
                    if !event.is_managed {
                        let _ = std::fs::remove_file(&path);
                        println!("released {}", event.bot.id);
                        return Ok(());
                    }
                    let managed = bot
                        .managed_bots()
                        .get_token(&event.bot.id)
                        .await
                        .map_err(std::io::Error::other)?;
                    let mut file = std::fs::OpenOptions::new()
                        .write(true)
                        .create(true)
                        .truncate(true)
                        .mode(0o600)
                        .open(&path)?;
                    file.write_all(managed.token.expose_secret().as_bytes())?;
                    println!("managing {} for {}", event.bot.id, event.user.id);
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
| `managed_mode_disabled` | 403 | Manage bots is off for your bot, or not available. Turn it on in NexoCreator. |
| `managed_bot_detached` | 403 | Your bot does not manage this bot, or no longer does. |
| `token_not_issued` | 409 | The managed bot has no current token. |
| `idempotency_key_reused` | 409 | The key was used for a different request. |
| `invalid_request` | 400 | The bot ID or body is invalid. |
| `upstream_unavailable` | 503 | Try again after `retry_after` seconds with the same key. |

The person sees their own errors in the creation sheet, such as a taken handle, an expired request or a reached limit. Your bot does not receive those failures. Only `upstream_unavailable` is safe to retry automatically.

## Related pages <a href="#related-pages" id="related-pages"></a>

- [Keyboards](../keyboards.md#reply-keyboards) covers reply keyboard settings.
- [Security](../security.md) explains how to store tokens.
- [Lifecycle](../lifecycle.md) describes transfers.
