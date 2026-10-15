---
description: "Run complete bot examples in TypeScript, Python or Rust."
---

# Examples

Run a complete example in your language, then adapt its handler to your bot. Each program lives in the public [ARX SDK repository](https://github.com/unusnullus/arx-sdks) and includes its setup instructions. Use its own README to install dependencies and set the bot token and API base URL.

## Send a message <a href="#send-a-message" id="send-a-message"></a>

This example sends one note to a chat the user has already started. Set `ARX_BOT_TOKEN` and `ARX_BOT_API_BASE_URL` as in the [quickstart](quickstart.md), set `CHAT_ID` from an incoming update, and save one `REQUEST_KEY` for this action. Run the same key and text again only when retrying that send.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const bot = new Client({ baseUrl: process.env.ARX_BOT_API_BASE_URL });
try {
  const message = await bot.sendText(
    process.env.CHAT_ID!, "Your note is saved.",
    { key: process.env.REQUEST_KEY! },
  );
  console.log(message.message_id);
} finally {
  await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(base_url=os.environ["ARX_BOT_API_BASE_URL"])) as bot:
    message = bot.send_text(
        os.environ["CHAT_ID"], "Your note is saved.",
        key=os.environ["REQUEST_KEY"],
    )
    print(message.message_id)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let bot = Client::new(Config::new(
        std::env::var("ARX_BOT_TOKEN")?,
        &std::env::var("ARX_BOT_API_BASE_URL")?,
        false,
    )?)?;
    let message = bot.send_text(
        &std::env::var("CHAT_ID")?, "Your note is saved.",
        Some(&std::env::var("REQUEST_KEY")?),
    ).await?;
    println!("{}", message.message_id());
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The SDK returns the sent message directly. The [request guide](making-requests.md) explains the HTTP envelope and how long replay results remain available.

## Choose a program <a href="#choose-a-program" id="choose-a-program"></a>

{% tabs %}
{% tab title="TypeScript" %}

| Example | What you will learn |
| --- | --- |
| [22 - Echo Bot](https://github.com/unusnullus/arx-sdks/tree/main/examples/typescript/22%20-%20Echo%20Bot) | Receive a message and send a reply. |
| [23 - Menus and Buttons](https://github.com/unusnullus/arx-sdks/tree/main/examples/typescript/23%20-%20Menus%20and%20Buttons) | Register commands and respond to buttons. |
| [24 - Webhooks](https://github.com/unusnullus/arx-sdks/tree/main/examples/typescript/24%20-%20Webhooks) | Receive signed webhook deliveries. |
| [25 - Files and Media](https://github.com/unusnullus/arx-sdks/tree/main/examples/typescript/25%20-%20Files%20and%20Media) | Upload and send files. |
| [26 - Streaming Replies](https://github.com/unusnullus/arx-sdks/tree/main/examples/typescript/26%20-%20Streaming%20Replies) | Show a draft while preparing a final reply. |
| [27 - Error Handling and Retries](https://github.com/unusnullus/arx-sdks/tree/main/examples/typescript/27%20-%20Error%20Handling%20and%20Retries) | Handle failures and repeat requests safely. |

{% endtab %}
{% tab title="Python" %}

| Example | What you will learn |
| --- | --- |
| [22 - Echo Bot](https://github.com/unusnullus/arx-sdks/tree/main/examples/python/22%20-%20Echo%20Bot) | Receive a message and send a reply. |
| [23 - Menus and Buttons](https://github.com/unusnullus/arx-sdks/tree/main/examples/python/23%20-%20Menus%20and%20Buttons) | Register commands and respond to buttons. |
| [24 - Webhooks](https://github.com/unusnullus/arx-sdks/tree/main/examples/python/24%20-%20Webhooks) | Receive signed webhook deliveries. |
| [25 - Files and Media](https://github.com/unusnullus/arx-sdks/tree/main/examples/python/25%20-%20Files%20and%20Media) | Upload and send files. |
| [26 - Streaming Replies](https://github.com/unusnullus/arx-sdks/tree/main/examples/python/26%20-%20Streaming%20Replies) | Show a draft while preparing a final reply. |
| [27 - Error Handling and Retries](https://github.com/unusnullus/arx-sdks/tree/main/examples/python/27%20-%20Error%20Handling%20and%20Retries) | Handle failures and repeat requests safely. |

{% endtab %}
{% tab title="Rust" %}

| Example | What you will learn |
| --- | --- |
| [22 - Echo Bot](https://github.com/unusnullus/arx-sdks/tree/main/examples/rust/22%20-%20Echo%20Bot) | Receive a message and send a reply. |
| [23 - Menus and Buttons](https://github.com/unusnullus/arx-sdks/tree/main/examples/rust/23%20-%20Menus%20and%20Buttons) | Register commands and respond to buttons. |
| [24 - Webhooks](https://github.com/unusnullus/arx-sdks/tree/main/examples/rust/24%20-%20Webhooks) | Receive signed webhook deliveries. |
| [25 - Files and Media](https://github.com/unusnullus/arx-sdks/tree/main/examples/rust/25%20-%20Files%20and%20Media) | Upload and send files. |
| [26 - Streaming Replies](https://github.com/unusnullus/arx-sdks/tree/main/examples/rust/26%20-%20Streaming%20Replies) | Show a draft while preparing a final reply. |
| [27 - Error Handling and Retries](https://github.com/unusnullus/arx-sdks/tree/main/examples/rust/27%20-%20Error%20Handling%20and%20Retries) | Handle failures and repeat requests safely. |

{% endtab %}
{% endtabs %}

## Run an example safely <a href="#run-an-example-safely" id="run-an-example-safely"></a>

Create a [TESTBOT](testing.md#testbot), start a conversation with it in Messenger, and keep its credentials separate from your regular bot. Use the chat ID from an incoming update. If you switch from polling to a webhook, stop the poller and follow the configuration steps in [Polling and webhooks](updates.md).

The examples use typed SDK methods. Keep stable idempotency keys for repeated actions, and add your own durable storage before deploying an example as a service. See [Making requests](making-requests.md) and the [go-live checklist](go-live-checklist.md).

For the commands and privacy behavior of ARX's own bots, read [Official bots](official-bots.md).
