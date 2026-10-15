---
description: "Test your handlers locally, then verify your bot in Messenger."
---

# Testing bots

Test your bot's replies and recovery behavior locally, then use a TESTBOT to check how users interact with it in Messenger. Use synthetic messages and files you own so your test fixtures contain no user data or real tokens.

## Local emulator <a href="#local-emulator" id="local-emulator"></a>

Install the Python package with the CLI extra from [SDKs and CLI](sdks.md#install-an-sdk). The emulator serves a local Bot API that works with all three clients. Start it in one terminal:

```sh
arx-nexo emulate --port 18092
```

The command prints one JSON line with `base_url`, `ui` and `credentials`. Open the `ui` address to chat with your bot as a test user. Keep the emulator running. In another terminal, activate the same Python environment and prepare a synthetic chat:

```sh
export ARX_BOT_API_BASE_URL='http://127.0.0.1:18092/bot-api/v1'
export ARX_BOT_TOKEN="$(python -c 'from arx_nexo.emulator import emulator_token; print(emulator_token())')"
export CHAT_ID="$(python - <<'PY'
from arx_nexo.testing import EmulatorControl

with EmulatorControl("http://127.0.0.1:18092") as control:
    print(control.start_chat(username="ANALOPEZ"))
PY
)"
```

This token is a fixed emulator credential. Never use a real token for local tests. Enable insecure loopback access explicitly in your client and keep that permission out of your live configuration.

## Connect any SDK <a href="#connect-any-sdk" id="connect-any-sdk"></a>

Each example below reads the emulator bot's identity and sends a message to the synthetic chat. Install the SDK and runtime for your language as described in [SDKs and CLI](sdks.md).

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const bot = new Client({
  token: process.env.ARX_BOT_TOKEN,
  baseUrl: process.env.ARX_BOT_API_BASE_URL,
  allowInsecureLoopback: true,
});
try {
  await bot.meGet();
  const message = await bot.sendText(process.env.CHAT_ID!, "Local test", {
    key: "local-test-1",
  });
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
from arx_nexo.emulator import emulator_token

with Client(Config(
    token=emulator_token(),
    base_url=os.environ["ARX_BOT_API_BASE_URL"],
    allow_insecure_loopback=True,
)) as bot:
    bot.me_get()
    message = bot.send_text(os.environ["CHAT_ID"], "Local test", key="local-test-1")
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
        true,
    )?)?;
    let query = [];
    bot.me_get(&query, None, None).await?;
    let message = bot.send_text(
        &std::env::var("CHAT_ID")?, "Local test", Some("local-test-1"),
    ).await?;
    println!("{}", message.message_id());
    Ok(())
}
```

{% endtab %}
{% endtabs %}

Run the same example twice. With the same key and payload, the returned message ID should stay the same. Use a new key for a new send.

## Isolate a test in Python <a href="#isolate-a-test-in-python" id="isolate-a-test-in-python"></a>

For an automated test, use a fresh emulator on an available port. Its context manager stops the server after the test:

```python
from arx_nexo import Client, Config
from arx_nexo.emulator import Emulator, emulator_token
from arx_nexo.testing import EmulatorControl

with Emulator(port=0) as emulator:
    with EmulatorControl(emulator.origin) as control:
        chat_id = control.start_chat(username="ANALOPEZ")
        with Client(Config(
            token=emulator_token(),
            base_url=emulator.base_url,
            allow_insecure_loopback=True,
        )) as bot:
            first = bot.send_text(chat_id, "Saved", key="saved-1")
            repeat = bot.send_text(chat_id, "Saved", key="saved-1")
            assert first.message_id == repeat.message_id
```

The TypeScript and Rust clients can connect to the same HTTP emulator during their own application tests. Both packages export `UpdateFactory` from the package root: `import { UpdateFactory } from "@arx/nexo"` in TypeScript and `use arx_nexo::UpdateFactory;` in Rust. Use it to exercise handlers without a network request.

## TESTBOT <a href="#testbot" id="testbot"></a>

Create a TESTBOT with `/newtestbot` in NexoCreator, then manage invited testers with `/testers`. A test version of Acme Support could use the handle `ACMESUPPORTTESTBOT`, if it is available. Your bot owner account and up to 50 invited testers can interact with it. Other users cannot discover or start it.

Use the environment's normal Bot API endpoint with the TESTBOT's own token. It has no `/test` URL prefix. Its effective sending limits and HTTPS webhook requirements still apply. A TESTBOT cannot become a regular bot, and another bot cannot reuse its file IDs. See [Environments](environments.md) when preparing a regular bot.

## Check your handlers <a href="#check-your-handlers" id="check-your-handlers"></a>

| Test | What your bot should do |
| --- | --- |
| Deliver an update twice, fail partway through a batch, then restart. | Preserve accepted work and avoid repeating important effects. |
| Stop the worker during a send whose outcome is unknown. | Resume with the stored action key and payload. |
| Send a valid webhook, then alter its bytes or signature. | Accept the valid delivery and reject the altered delivery before running a handler. |
| Return a retryable failure or a rate limit. | Respect the delay and keep other chats responsive. |
| Send unsupported input, emoji near entity boundaries and oversized files. | Explain valid input to the user and handle the API error without crashing. |
| Block and restart the bot from Messenger. | Stop new interaction while blocked and handle the new Start flow. |

The emulator does not decode real media or reproduce every network and Messenger behavior. Verify your own uploads, downloads, rendering, keyboard interactions, file processing and token rotation with a TESTBOT. Test invalid signatures locally before configuring live delivery.

Follow [Polling and webhooks](updates.md) for acknowledgement rules and the [go-live checklist](go-live-checklist.md) before inviting users.
