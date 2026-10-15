---
description: "Create a Nexo bot and send its first reply with any of the three SDKs."
---

# Create your first bot

Create a bot that replies "Hello from Nexo!" when you send it a message. You need an ARX account, Messenger, and [the SDK for your language](sdks.md#install-an-sdk).

## Create the bot <a href="#create-the-bot" id="create-the-bot"></a>

Open `@NEXOCREATOR` in Messenger and send `/newbot`. Read and accept the developer terms shown there, then choose a display name and an available handle. For example, a support bot could be named **Acme Support** with the handle `ACMESUPPORTBOT`. NexoCreator checks the name and handle for you. See [NexoCreator](nexocreator.md) for account requirements and creation limits.

Choose **View token** and save the credential immediately. You can reveal it only once, within 3,600 seconds (60 minutes) by default. This is the reveal window, not the token's lifetime. If you miss it or lose the credential, rotate the token in NexoCreator. [Authentication & tokens](authentication.md) explains storage and replacement.

Set these variables in the terminal where you will run the program:

```sh
printf 'Bot token: '
read -rs ARX_BOT_TOKEN
export ARX_BOT_TOKEN
printf '\n'
export ARX_BOT_API_BASE_URL='https://signal.arx.pro/bot-api/v1'
```

Regular bots and TESTBOTs use this same endpoint. See [Environments](environments.md).

## Start a conversation <a href="#start-a-conversation" id="start-a-conversation"></a>

Find your bot in Messenger and choose **Start**. The user must start the conversation before your bot can reply. Its first `message` update contains `/start`; `message.chat.id` identifies the conversation. Keep chat IDs and user IDs separate.

## Run the reply loop <a href="#run-the-reply-loop" id="run-the-reply-loop"></a>

Save the program for your language and run it using the SDK setup from [SDKs and CLI](sdks.md). Use one running poller for this bot. If you configured a webhook previously, [remove it before polling](updates.md).

{% tabs %}
{% tab title="TypeScript" %}

Save as `hello.ts` in your Node.js ESM project and run `npx tsx hello.ts`.

```ts
import { Client } from "@arx/nexo";

const bot = new Client({ baseUrl: process.env.ARX_BOT_API_BASE_URL });
let offset: string | undefined;
try {
  while (true) {
    const batch = await bot.updatesGet({
      query: { timeout_seconds: 30, ...(offset ? { offset } : {}) },
    });
    for (const update of batch.updates) {
      if (update.type === "message" && update.message) {
        await bot.sendText(update.message.chat.id, "Hello from Nexo!", {
          key: `hello-${update.update_id}`,
        });
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

Save as `hello.py` and run `python hello.py`.

```python
import os
from arx_nexo import Client, Config

with Client(Config(base_url=os.environ["ARX_BOT_API_BASE_URL"])) as bot:
    offset: str | None = None
    while True:
        batch = bot.updates_get(offset=offset, timeout_seconds=30)
        for update in batch.updates:
            if update.type == "message" and update.message is not None:
                bot.send_text(
                    update.message.chat.id,
                    "Hello from Nexo!",
                    key=f"hello-{update.update_id}",
                )
            offset = str(int(update.update_id) + 1)
```

{% endtab %}
{% tab title="Rust" %}

Save as `src/main.rs` in your Cargo application. Add `tokio` with the `macros` and `rt-multi-thread` features to its dependencies, then run `cargo run`.

```rust
use arx_nexo::{Client, Config};

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
            if let Some(message) = update.message() {
                let key = format!("hello-{}", update.update_id());
                bot.send_text(&message.chat().id, "Hello from Nexo!", Some(&key)).await?;
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

Send another message in Messenger. Your bot should reply with "Hello from Nexo!".

The next polling request supplies the last processed `update_id` plus one. That acknowledges every update up to that ID, so advance the offset only after you finish processing each update. These programs keep the offset in memory. Their stable reply key lets a repeated update replay the same send while its idempotency record remains valid, but it does not protect other work your handler performs.

Before deploying, add durable update handling and graceful shutdown using the [updates guide](updates.md). Then work through [Testing](testing.md) and the [go-live checklist](go-live-checklist.md).
