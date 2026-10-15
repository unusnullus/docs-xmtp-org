---
description: "Install the TypeScript, Python or Rust SDK and use the development CLI."
---

# SDKs and CLI

Connect your bot with a typed SDK in TypeScript, Python or Rust. Each SDK provides named API methods, polling helpers, webhook verification, file uploads and downloads. The standalone `arx-nexo` CLI includes an emulator and works with bots written in any language.

## Install an SDK <a href="#install-an-sdk" id="install-an-sdk"></a>

Clone the public [ARX SDK repository](https://github.com/unusnullus/arx-sdks):

```sh
git clone https://github.com/unusnullus/arx-sdks.git
cd arx-sdks
export ARX_SDKS_ROOT="$PWD"
```

Keep `ARX_SDKS_ROOT` set in this terminal when you move to your application's directory. The setup commands below use it to find the SDK checkout.

{% tabs %}
{% tab title="TypeScript" %}

The npm package is `@arx/nexo`. Use Node.js 22 or later with ESM.

```sh
npm --prefix "$ARX_SDKS_ROOT/SDKs/bot/typescript" ci
npm --prefix "$ARX_SDKS_ROOT/SDKs/bot/typescript" run build
```

Create an application directory, then install the client and the tools used to run the TypeScript examples:

```sh
mkdir acme-support-typescript
cd acme-support-typescript
npm init -y
npm install "$ARX_SDKS_ROOT/SDKs/bot/typescript"
npm install --save-dev typescript@5 tsx @types/node@22
npm pkg set type=module
```

The last command enables ESM in your application's `package.json`.

{% endtab %}
{% tab title="Python" %}

The distribution is `arx-nexo`. You import it as `arx_nexo`. Use Python 3.10 or later.

```sh
python3 -m venv .venv
. .venv/bin/activate
python -m pip install "$ARX_SDKS_ROOT/SDKs/bot/python"
```

{% endtab %}
{% tab title="Rust" %}

The crate is `arx-nexo`. You import it as `arx_nexo`. Use Rust 1.85 or later. Create a Cargo application, then add the local SDK and the dependencies used by the examples:

```sh
cargo new acme-support-bot
cd acme-support-bot
cargo add arx-nexo --path "$ARX_SDKS_ROOT/SDKs/bot/rust"
cargo add tokio@1 --features macros,rt-multi-thread
cargo add serde_json@1
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
    let query = [];
    let identity = bot.me_get(&query, None, None).await?;
    println!("{}", identity.username);
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The API calls the public name field `username`. The documentation calls this a handle. Avoid logging the full identity response or credential configuration.

Rust's generated read methods take a query slice, an optional body and an optional request key. `me.get` needs an empty query and neither optional argument.

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

For FastAPI, install `python -m pip install "$ARX_SDKS_ROOT/SDKs/bot/python[fastapi]"` in your Python environment, then import its adapter:

```python
from arx_nexo.adapters.fastapi import fastapi_webhook
```

For aiohttp, install `python -m pip install "$ARX_SDKS_ROOT/SDKs/bot/python[aiohttp]"`, then import its adapter:

```python
from arx_nexo.adapters.aiohttp import aiohttp_webhook
```

{% endtab %}
{% tab title="Rust" %}

From your Cargo application's directory, enable the `axum` feature on your `arx-nexo` dependency:

```sh
cargo add arx-nexo --path "$ARX_SDKS_ROOT/SDKs/bot/rust" --features axum
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

The `arx-nexo` command lives in `CLI/` at the repository root. It requires Rust 1.85 or later and works independently of the SDK you choose. Install it from your clone:

```sh
cargo install --path "$ARX_SDKS_ROOT/CLI"
arx-nexo --help
```

You can also run it from source:

```sh
cargo run --release --manifest-path "$ARX_SDKS_ROOT/CLI/Cargo.toml" -- whoami
```

Live requests read `ARX_BOT_TOKEN` from the environment. Pass `--base-url` before the subcommand, or set `ARX_BOT_API_BASE_URL`. Tokens are never command arguments. YAML support is included.

Prepare those variables with the [quickstart setup](quickstart.md#create-the-bot), and set `CHAT_ID` to `message.chat.id` from an incoming update in a chat you have started with the bot.

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
| `emulator-token` | Print its fixed synthetic token. |
| `new DIR --lang typescript\|python\|rust` | Create an echo project configured for the emulator. |

For example, save this as `commands.yaml` and run `arx-nexo commands push commands.yaml`:

```yaml
commands:
  - command: help
    description: Show available commands
```

Registering a command adds it to the menu. Your program must still implement the handler. See [Commands & profile](commands-and-profile.md) for scope and localization, and [Testing](testing.md) to connect any SDK to the emulator.

## Create an echo project <a href="#create-an-echo-project" id="create-an-echo-project"></a>

From a directory where you want to create a new project:

```sh
arx-nexo new acme-support --lang typescript
cd acme-support
```

Choose `python` or `rust` instead for the same tutorial in that language. The generated README has three steps: start the emulator, install dependencies and run the bot. Its `.env` already contains synthetic local settings. See [Testing](testing.md) for the emulator UI and HTTP controls. SDK dependencies point to your clone; set `ARX_SDKS_ROOT` before `new` if you moved it. An existing destination is rejected without overwriting files.
