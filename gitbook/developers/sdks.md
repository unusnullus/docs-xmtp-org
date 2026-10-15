---
description: "Install the TypeScript, Python or Rust SDK and use the development CLI."
---

# SDKs and CLI

Connect your bot with a typed SDK in TypeScript, Python or Rust. Each SDK provides named API methods, polling helpers, webhook verification, file uploads and downloads. The Python package also includes a CLI and an emulator you can use with any of the clients.

## Install an SDK <a href="#install-an-sdk" id="install-an-sdk"></a>

Clone the public [ARX SDK repository](https://github.com/unusnullus/arx-sdks):

```sh
git clone https://github.com/unusnullus/arx-sdks.git
cd arx-sdks
```

Run the setup commands below from the checkout's root, then add the client to your own application.

{% tabs %}
{% tab title="TypeScript" %}

The npm package is `@arx/nexo`. Use Node.js 22 or later with ESM.

```sh
npm --prefix SDKs/bot/typescript ci
npm --prefix SDKs/bot/typescript run build
```

In your application directory, run `npm install /absolute/path/to/arx-sdks/SDKs/bot/typescript`. Install `typescript`, `tsx` and `@types/node` as development dependencies to run the TypeScript quickstart. Set `"type": "module"` in your application's `package.json`.

{% endtab %}
{% tab title="Python" %}

The distribution is `arx-nexo`. You import it as `arx_nexo`. Use Python 3.10 or later.

```sh
python3 -m venv .venv
. .venv/bin/activate
python -m pip install './SDKs/bot/python[cli]'
```

The `cli` extra installs YAML support for the command and profile tools. You can omit it if you only need the client.

{% endtab %}
{% tab title="Rust" %}

The crate is `arx-nexo`. You import it as `arx_nexo`. Use Rust 1.85 or later. Add this dependency to your application's `Cargo.toml`, replacing the path with your checkout:

```toml
[dependencies]
arx-nexo = { path = "/absolute/path/to/arx-sdks/SDKs/bot/rust" }
tokio = { version = "1", features = ["macros", "rt-multi-thread"] }
serde_json = "1"
```

{% endtab %}
{% endtabs %}

## Connect <a href="#connect" id="connect"></a>

Set `ARX_BOT_TOKEN` and `ARX_BOT_API_BASE_URL` as described in [Authentication & tokens](authentication.md). Set the base URL explicitly so your program uses the intended [environment](environments.md).

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const bot = new Client({ baseUrl: process.env.ARX_BOT_API_BASE_URL });
try {
  const identity = await bot.meGet();
  console.log(identity.username);
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
    identity = bot.me_get()
    print(identity.username)
```

Use `AsyncClient` with an async context manager if your application uses asyncio.

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
    let identity = bot.me_get(&[], None, None).await?;
    println!("{}", identity.username);
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The API calls the public name field `username`. The documentation calls this a handle. Avoid logging the full identity response or credential configuration.

## Choose a method and receive updates <a href="#choose-a-method-and-receive-updates" id="choose-a-method-and-receive-updates"></a>

Python and Rust use method names such as `messages_send`. TypeScript uses `messagesSend`. For a plain text reply, use `send_text` in Python or Rust, or `sendText` in TypeScript. The [API reference](api.md) shows typed calls for individual operations.

All three SDKs provide a `Polling` helper. Python also provides `AsyncPolling`. Each client offers webhook verification and receiver helpers. Follow [Polling and webhooks](updates.md) for complete setup, verification and acknowledgement examples.

## Webhook adapters <a href="#webhook-adapters" id="webhook-adapters"></a>

Choose an adapter for the web framework your application uses. The adapter connects your HTTP route to an SDK webhook receiver.

{% tabs %}
{% tab title="TypeScript" %}

Install the framework you use alongside `@arx/nexo`. The package exports these adapters:

```ts
import { expressWebhook } from "@arx/nexo/adapters/express";
import { registerFastifyWebhook } from "@arx/nexo/adapters/fastify";
import { honoWebhook } from "@arx/nexo/adapters/hono";
```

{% endtab %}
{% tab title="Python" %}

For FastAPI, install `python -m pip install './SDKs/bot/python[fastapi]'` from the repository checkout, then import its adapter:

```python
from arx_nexo.adapters.fastapi import fastapi_webhook
```

For aiohttp, install `python -m pip install './SDKs/bot/python[aiohttp]'`, then import its adapter:

```python
from arx_nexo.adapters.aiohttp import aiohttp_webhook
```

{% endtab %}
{% tab title="Rust" %}

Enable the `axum` feature on your `arx-nexo` dependency:

```toml
arx-nexo = { path = "/absolute/path/to/arx-sdks/SDKs/bot/rust", features = ["axum"] }
```

Import the adapter in your application:

```rust
use arx_nexo::adapters::axum::axum_webhook;
```

{% endtab %}
{% endtabs %}

## Retries and idempotency <a href="#retries-and-idempotency" id="retries-and-idempotency"></a>

The clients retry eligible reads and idempotent mutations after transport failures, HTTP 429 and 5xx responses, `idempotency_in_progress`, or `polling_conflict`. They bound the number of attempts and total time, respect server retry delays, and retain the same key and serialized request body.

Multipart requests containing immutable bytes can retry with the same parts and boundary. Paths and readers are streamed once. For an application retry, reopen the source and preserve the original key and payload. Check [Errors](errors.md) before deciding whether to retry a failure.

If retries stop without a known outcome, keep the original key for reconciliation. A new key can send a duplicate message. See [Making requests](making-requests.md#idempotency).

## Sending queues <a href="#sending-queues" id="sending-queues"></a>

Each client queues sends per chat and shares a global sending limiter. Queue time counts toward the request deadline. A rate limit pauses sends in its scope. If a local queue fills up, TypeScript and Python raise `QueueFullError`. Rust returns `Error::QueueFull`.

Load your bot's effective quotas with Python or Rust `refresh_send_limits()`, or TypeScript `refreshSendLimits()`. When several processes send for one bot, coordinate their rates in your application. See [Limits](limits.md) for the effective policy.

## CLI <a href="#cli" id="cli"></a>

The `arx-nexo` command comes from the Python package. You can also run it as `python -m arx_nexo`. Live requests read `ARX_BOT_TOKEN` from the environment. Pass `--base-url` before the subcommand, or set `ARX_BOT_API_BASE_URL`.

```sh
arx-nexo --base-url "$ARX_BOT_API_BASE_URL" whoami
arx-nexo --base-url "$ARX_BOT_API_BASE_URL" send \
  --chat-id "$CHAT_ID" --text 'Hello' --idempotency-key hello-1
```

Use a chat you have started with the bot. Repeat `hello-1` only when retrying that same send.

| Command | What you can do |
| --- | --- |
| `whoami` | Read your bot's identity. |
| `send --chat-id ID --text TEXT` | Send text with an optional `--idempotency-key`. |
| `commands push FILE` | Set commands from YAML. |
| `profile push FILE` | Update profile fields from YAML. |
| `webhook set`, `webhook get`, `webhook delete` | Configure and inspect update delivery. |
| `webhook test` | Ask Nexo to test the configured webhook. |
| `webhook test --url URL` | Send a synthetic signed request directly to your receiver. |
| `verify-signature --body-file FILE --signature VALUE` | Check recorded delivery bytes. |
| `emulate --port PORT` | Start the local emulator. |

For example, save this as `commands.yaml` and run `arx-nexo commands push commands.yaml`:

```yaml
commands:
  - command: help
    description: Show available commands
```

Registering a command adds it to the menu. Your program must still implement the handler. See [Commands & profile](commands-and-profile.md) for scope and localization, and [Testing](testing.md) to connect any SDK to the emulator.
