---
description: "Run the Business endpoint runtime, register its keys, decrypt Business messages and send automatic replies."
---

# Business endpoint

Business chats are end-to-end encrypted, so your bot needs its own encryption endpoint for each [Business connection](business-connections.md). The SDKs ship a Business runtime that creates and keeps the endpoint keys on your server, registers the public keys with ARX, decrypts incoming envelopes and encrypts replies. Your private keys never leave the runtime.

This page shows how to set up the runtime and run a bot that answers new messages with an automatic reply.

## How the runtime works <a href="#how-the-runtime-works" id="how-the-runtime-works"></a>

The runtime is a Rust program in the SDK repository. The TypeScript and Python SDKs start it as a child process through `BusinessRuntime`. Rust programs use the runtime crate directly.

| Step | TypeScript | Python | Rust crate |
| --- | --- | --- | --- |
| Open the state for one connection | `BusinessRuntime.open` | `BusinessRuntime(...)` | `EndpointRuntime::new` |
| Register the endpoint | `register` | `register` | `register` |
| Decrypt an envelope | `decodeUpdateFromApi` | `decode_update_from_api` | `decode_update_from_api` |
| Send a text reply | `sendText` | `send_text` | `send_text` |
| Add one-time prekeys | `uploadPrekeys` | `upload_prekeys` | `upload_prekeys` |
| Close | `close` | `close` | Drop the value |

Use one runtime per connection. Each one is bound to the connection ID, your bot ID and the account owner's user ID, and refuses state that belongs to another binding.

## Set up the runtime <a href="#set-up-the-runtime" id="set-up-the-runtime"></a>

The runtime runs on Unix hosts and needs Rust 1.94.1 to build. Build it from your SDK checkout:

```sh
cargo build --release --locked --manifest-path "$ARX_SDKS_ROOT/SDKs/bot/runtime/Cargo.toml"
```

The program is `$ARX_SDKS_ROOT/SDKs/bot/runtime/target/release/arx-nexo-business-runtime`. Rust programs add the crate instead:

```sh
cargo add arx-nexo-business-runtime --path "$ARX_SDKS_ROOT/SDKs/bot/runtime"
cargo add zeroize@1
```

Create a state directory and a local state key:

```sh
install -d -m 700 /srv/acme-support/business
head -c 32 /dev/urandom > /srv/acme-support/business.key
chmod 600 /srv/acme-support/business.key
```

The runtime encrypts its state with this 32-byte key. Follow these rules:

- Keep the key in your secret store, outside the state directory and outside backups of that directory. The runtime accepts it only through its API, never from a command-line argument or environment variable.
- Use a separate directory with mode `0700` for each connection. The examples create `<state directory>/<connection ID>`.
- Run one process per bot token and state directory. The runtime takes a writer lock and fails if another process holds it.
- Keep both the key and the directory across restarts. Never generate new keys after a timeout or an uncertain response. The runtime saves keys before it sends them, so a retry registers the same keys.

If you lose the key or the state, your bot cannot decrypt that connection again. Ask the account owner to disconnect your bot and connect it again.

## Register the endpoint <a href="#register-the-endpoint" id="register-the-endpoint"></a>

When a `business_connection` update arrives, read the connection. If its `status` is `pending_endpoint`, call `register`, which uses [`business_endpoint.register`](../reference/business.md#operation-business-endpoint-register). The runtime generates an identity key, a signed prekey and up to 100 one-time prekeys, signs a proof of possession and sends only the public parts.

`register` returns `status`, `installation_token`, `certificate_sequence` and `prekeys_remaining`. `pending_certificate` is the expected result: the account owner still has to approve your certificate in ARX. The connection becomes `active` after that approval. A registration result is information about your endpoint; it does not permit any operation by itself.

Each contact or owner device that opens a session uses one of your one-time prekeys. Read `prekeys_remaining` from [`business_endpoint.get`](../reference/business.md#operation-business-endpoint-get) and call `upload_prekeys` with a new idempotency key to add 1 to 100 more. When none remain, sessions use the signed prekey.

## Receive messages and reply <a href="#receive-messages-and-reply" id="receive-messages-and-reply"></a>

Pass each `business_envelope` update to `decode_update_from_api`. The runtime looks up the sender's device keys through [`business_owner_devices.list`](../reference/business.md#operation-business-owner-devices-list) and [`business_peer_devices.list`](../reference/business.md#operation-business-peer-devices-list), verifies and decrypts the envelope, and saves the result before it returns a typed update. Confirm the update only after your own handler finishes.

To reply, call `send_text` with the chat ID, the text and an idempotency key. The runtime reads the current device lists, encrypts one copy for each contact device and each owner device, and sends them together. The receipt has `message_ref`, `accepted` and `rejected`. It confirms that ARX accepted the send, not that each device received it.

Derive the key from the message you answer, such as `auto-<chat ID>-<message ID>`. After a timeout or restart, the same key returns the original receipt without sending a second reply.

## Complete example <a href="#complete-example" id="complete-example"></a>

This bot registers an endpoint for each new connection and answers every new text message from a contact with a fixed automatic reply. It ignores messages the account owner sends and messages from bots.

Set these environment variables before running it:

| Variable | Value |
| --- | --- |
| `ARX_BOT_TOKEN` | Your bot token. |
| `BUSINESS_RUNTIME` | The absolute path of `arx-nexo-business-runtime` (TypeScript and Python). |
| `BUSINESS_STATE_DIR` | The absolute path of the state directory. |
| `BUSINESS_STATE_KEY` | The absolute path of the 32-byte key file. |

The program keeps its polling offset in memory. Store the offset durably before you run it in production.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { mkdir, readFile } from "node:fs/promises";
import { join } from "node:path";
import { setTimeout as sleep } from "node:timers/promises";
import { BusinessRuntime, Client, type Models } from "@arx/nexo";

function required(name: string): string {
	const value = process.env[name];
	if (!value) throw new Error(`Set ${name}`);
	return value;
}
const token = required("ARX_BOT_TOKEN");
const binary = required("BUSINESS_RUNTIME");
const stateDir = required("BUSINESS_STATE_DIR");
const keyFile = required("BUSINESS_STATE_KEY");
const baseUrl = "https://signal.arx.pro/bot-api/v1";
const runtimeUrl = `${baseUrl}/`;
const reply = "Thanks for your message. I will answer as soon as I can.";
const localStateKey = new Uint8Array(await readFile(keyFile));
const bot = new Client({ token, baseUrl });
const runtimes = new Map<string, BusinessRuntime>();
const failures = new Map<string, number>();

async function runtimeFor(connectionId: string, botId: string): Promise<BusinessRuntime> {
	const open = runtimes.get(connectionId);
	if (open) return open;
	const connection = await bot.businessConnectionsGet(connectionId);
	const directory = join(stateDir, connectionId);
	await mkdir(directory, { mode: 0o700, recursive: true });
	const runtime = await BusinessRuntime.open({
		binary,
		directory,
		localStateKey,
		binding: {
			connection_id: connectionId,
			bot_id: botId,
			owner_registration_id: connection.user.id,
		},
	});
	runtimes.set(connectionId, runtime);
	return runtime;
}

async function handle(update: Models.Update, me: Models.Bot): Promise<void> {
	const changed = update.type === "business_connection" ? update.business_connection : undefined;
	if (changed) {
		const connection = await bot.businessConnectionsGet(changed.id);
		if (connection.status === "pending_endpoint") {
			const runtime = await runtimeFor(changed.id, me.id);
			const endpoint = await runtime.register(runtimeUrl, token);
			console.log("endpoint", changed.id, endpoint.status);
		}
		return;
	}
	const envelope = update.type === "business_envelope" ? update.business_envelope : undefined;
	if (!envelope) return;
	const connectionId = envelope.business_connection_id;
	const runtime = await runtimeFor(connectionId, me.id);
	try {
		const event = await runtime.decodeUpdateFromApi(
			runtimeUrl,
			token,
			update as unknown as Record<string, unknown>,
		);
		if (event.type !== "business_message") return;
		const message = event.business_message;
		if (message.is_outgoing || message.sender_user.is_bot) return;
		const receipt = await runtime.sendText(runtimeUrl, token, {
			chat_id: message.chat.id,
			text: reply,
			key: `auto-${message.chat.id}-${message.message_id}`,
		});
		console.log("reply", receipt.message_ref, receipt.accepted.length);
	} catch (error) {
		runtimes.delete(connectionId);
		await runtime.close().catch(() => undefined);
		throw error;
	}
}

const me = await bot.meGet();
let offset: string | undefined;
try {
	for (;;) {
		const batch = await bot.updatesGet({
			query: {
				timeout_seconds: 25,
				allowed_updates: "business_connection,business_envelope",
				...(offset === undefined ? {} : { offset }),
			},
		});
		for (const update of batch.updates) {
			try {
				await handle(update, me);
			} catch (error) {
				const attempts = (failures.get(update.update_id) ?? 0) + 1;
				failures.set(update.update_id, attempts);
				console.error("failed", update.update_id, (error as Error).name, attempts);
				if (attempts < 5) {
					await sleep(3000);
					break;
				}
			}
			failures.delete(update.update_id);
			offset = (BigInt(update.update_id) + 1n).toString();
		}
	}
} finally {
	for (const runtime of runtimes.values()) await runtime.close();
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
import time
from pathlib import Path
from typing import Any

from arx_nexo import BusinessRuntime, Client, Config

BASE_URL = "https://signal.arx.pro/bot-api/v1"
RUNTIME_URL = BASE_URL + "/"
REPLY = "Thanks for your message. I will answer as soon as I can."

token = os.environ["ARX_BOT_TOKEN"]
binary = os.environ["BUSINESS_RUNTIME"]
state_dir = Path(os.environ["BUSINESS_STATE_DIR"])
local_state_key = Path(os.environ["BUSINESS_STATE_KEY"]).read_bytes()
runtimes: dict[str, BusinessRuntime] = {}
failures: dict[str, int] = {}

def runtime_for(bot: Client, bot_id: str, connection_id: str) -> BusinessRuntime:
    if connection_id not in runtimes:
        connection = bot.business_connections_get(connection_id)
        directory = state_dir / connection_id
        directory.mkdir(mode=0o700, exist_ok=True)
        runtimes[connection_id] = BusinessRuntime(
            binary=binary,
            directory=directory,
            local_state_key=local_state_key,
            binding={
                "connection_id": connection_id,
                "bot_id": bot_id,
                "owner_registration_id": connection.user.id,
            },
        )
    return runtimes[connection_id]

def handle(bot: Client, bot_id: str, raw: dict[str, Any]) -> None:
    if raw["type"] == "business_connection":
        connection_id = raw["business_connection"]["id"]
        if bot.business_connections_get(connection_id).status == "pending_endpoint":
            endpoint = runtime_for(bot, bot_id, connection_id).register(RUNTIME_URL, token)
            print("endpoint", connection_id, endpoint["status"])
        return
    if raw["type"] != "business_envelope":
        return
    connection_id = raw["business_envelope"]["business_connection_id"]
    runtime = runtime_for(bot, bot_id, connection_id)
    try:
        event = runtime.decode_update_from_api(RUNTIME_URL, token, raw)
        if event["type"] != "business_message":
            return
        message = event["business_message"]
        if message["is_outgoing"] or message["sender_user"]["is_bot"]:
            return
        chat_id = message["chat"]["id"]
        receipt = runtime.send_text(
            RUNTIME_URL,
            token,
            chat_id=chat_id,
            text=REPLY,
            key=f"auto-{chat_id}-{message['message_id']}",
        )
        print("reply", receipt["message_ref"], len(receipt["accepted"]))
    except Exception:
        runtimes.pop(connection_id, None)
        runtime.close()
        raise

with Client(Config(token=token, base_url=BASE_URL)) as bot:
    bot_id = bot.me_get().id
    offset = None
    try:
        while True:
            batch = bot.updates_get(
                offset=offset,
                timeout_seconds=25,
                allowed_updates="business_connection,business_envelope",
            )
            for update in batch.updates:
                try:
                    handle(bot, bot_id, update.model_dump(mode="json", exclude_unset=True))
                except Exception as error:
                    attempts = failures.get(update.update_id, 0) + 1
                    failures[update.update_id] = attempts
                    print("failed", update.update_id, type(error).__name__, attempts)
                    if attempts < 5:
                        time.sleep(3)
                        break
                failures.pop(update.update_id, None)
                offset = str(int(update.update_id) + 1)
    finally:
        for runtime in runtimes.values():
            runtime.close()
```

{% endtab %}
{% tab title="Rust" %}

```rust
use std::{
    collections::HashMap,
    error::Error,
    os::unix::fs::DirBuilderExt,
    path::{Path, PathBuf},
    time::Duration,
};

use arx_nexo::{Client, Config, models};
use arx_nexo_business_runtime::{
    Error as RuntimeError,
    api::TextSend,
    incoming::{RawBusinessUpdate, TypedBusinessUpdate},
    key_material::ConnectionBinding,
    registration::{BotHttp, EndpointRuntime},
    storage::{EncryptedStore, LocalStateKey, SqliteStateStore},
};
use serde_json::Value;
use zeroize::Zeroizing;

type Runtime = EndpointRuntime<SqliteStateStore, LocalStateKey>;
type Result<T> = std::result::Result<T, Box<dyn Error>>;

const BASE_URL: &str = "https://signal.arx.pro/bot-api/v1";
const REPLY: &str = "Thanks for your message. I will answer as soon as I can.";

struct Endpoint {
    bot: Client,
    http: BotHttp,
    bot_id: String,
    state_dir: PathBuf,
    key: [u8; 32],
    runtimes: HashMap<String, Runtime>,
}

async fn open_runtime<'a>(
    runtimes: &'a mut HashMap<String, Runtime>,
    bot: &Client,
    bot_id: &str,
    state_dir: &Path,
    key: [u8; 32],
    connection_id: &str,
) -> Result<&'a mut Runtime> {
    if !runtimes.contains_key(connection_id) {
        let connection = bot.business_connections_get(connection_id, &[], None, None).await?;
        let binding = ConnectionBinding {
            connection_id: connection_id.to_owned(),
            bot_id: bot_id.to_owned(),
            owner_registration_id: connection.user.id.clone(),
        };
        let directory = state_dir.join(connection_id);
        std::fs::DirBuilder::new().mode(0o700).recursive(true).create(&directory)?;
        let store = SqliteStateStore::open(&directory).await?;
        let encrypted = EncryptedStore::new(store, LocalStateKey::new(key), &binding)?;
        runtimes.insert(connection_id.to_owned(), EndpointRuntime::new(encrypted, binding)?);
    }
    Ok(runtimes.get_mut(connection_id).expect("runtime"))
}

impl Endpoint {
    async fn handle(&mut self, update: &Value) -> Result<()> {
        let Endpoint { bot, http, bot_id, state_dir, key, runtimes } = self;
        if update["type"] == "business_connection" {
            let id = update["business_connection"]["id"].as_str().ok_or("missing ID")?;
            let connection = bot.business_connections_get(id, &[], None, None).await?;
            if matches!(connection.status, models::BusinessConnectionStatus::PendingEndpoint) {
                let runtime = open_runtime(runtimes, bot, bot_id, state_dir, *key, id).await?;
                let observation = runtime.register(&mut *http, 100).await?;
                println!("endpoint {id} {:?}", observation.status);
            }
            return Ok(());
        }
        if update["type"] != "business_envelope" {
            return Ok(());
        }
        let raw: RawBusinessUpdate = serde_json::from_value(update.clone())?;
        let connection_id = raw.business_envelope.business_connection_id.clone();
        let runtime = open_runtime(runtimes, bot, bot_id, state_dir, *key, &connection_id).await?;
        let event = runtime.decode_update_from_api(&mut *http, &raw).await?;
        let TypedBusinessUpdate::Message { business_message: message, .. } = event else {
            return Ok(());
        };
        if message.is_outgoing || message.sender_user.is_bot {
            return Ok(());
        }
        let input = TextSend {
            chat_id: message.chat.id.clone(),
            text: REPLY.to_owned(),
            key: format!("auto-{}-{}", message.chat.id, message.message_id),
            reply_to: None,
        };
        let receipt = runtime.send_text(&mut *http, &input).await?;
        println!("reply {} {}", receipt.message_ref, receipt.accepted.len());
        Ok(())
    }
}

#[tokio::main]
async fn main() -> Result<()> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let state_dir = PathBuf::from(std::env::var("BUSINESS_STATE_DIR")?);
    let key: [u8; 32] = std::fs::read(std::env::var("BUSINESS_STATE_KEY")?)?
        .try_into()
        .map_err(|_| "the state key must contain 32 bytes")?;
    let http = BotHttp::new(&format!("{BASE_URL}/"), Zeroizing::new(token.clone()))?;
    let bot = Client::new(Config::new(token, BASE_URL, false)?)?;
    let bot_id = bot.me_get(&[], None, None).await?.id;
    let mut endpoint = Endpoint { bot, http, bot_id, state_dir, key, runtimes: HashMap::new() };
    let mut offset: Option<String> = None;
    let mut failures: HashMap<String, u32> = HashMap::new();
    loop {
        let mut query = vec![
            ("timeout_seconds", "25"),
            ("allowed_updates", "business_connection,business_envelope"),
        ];
        if let Some(offset) = &offset {
            query.push(("offset", offset.as_str()));
        }
        // Read raw JSON so the runtime receives each envelope exactly as delivered.
        let batch: Value = endpoint.bot.call("updates.get", &[], &query, None, None).await?;
        let updates = batch["updates"].as_array().cloned().unwrap_or_default();
        for update in &updates {
            let id = update["update_id"].as_str().ok_or("missing update ID")?.to_owned();
            if let Err(error) = endpoint.handle(update).await {
                let unsupported = error.downcast_ref::<RuntimeError>()
                    == Some(&RuntimeError::UnsupportedContent);
                let attempts = failures.entry(id.clone()).or_insert(0);
                *attempts += 1;
                eprintln!("failed {id} {error} {attempts}");
                endpoint.runtimes.clear();
                if !unsupported && *attempts < 5 {
                    tokio::time::sleep(Duration::from_secs(3)).await;
                    break;
                }
            }
            failures.remove(&id);
            offset = Some((id.parse::<u64>()? + 1).to_string());
        }
    }
}
```

{% endtab %}
{% endtabs %}

Each language retries a failing update up to five times, then logs it and moves on. Rust skips content the runtime does not support at once. See [Handle failures](#handle-failures).

## Handle failures <a href="#handle-failures" id="handle-failures"></a>

- **Uncertain sends.** If `send_text` times out or the process stops, call it again with the same chat, text and key. The runtime returns the saved receipt or completes the original send. Do not change the text for an existing key.
- **Runtime errors in TypeScript and Python.** `BusinessRuntimeError` carries no details, so that private values never reach your logs. The child process stops after an error. Close the runtime and open it again with the same directory and key, as the example does.
- **Runtime errors in Rust.** `Error` has variants such as `ConnectionInactive`, `RightMissing`, `WindowClosed`, `NotInScope`, `UnsupportedContent`, `UntrustedIdentity` and `WriterBusy`. Messages never contain keys, SQL or message text.
- **Unsupported content.** The runtime decodes text messages, edits and deletions. Other content fails with `UnsupportedContent` in Rust and `BusinessRuntimeError` in TypeScript and Python. Record the `envelope_id` and continue.
- **Changed identities.** The runtime pins each sender's identity key. If a device's key changes, decryption fails with `UntrustedIdentity` instead of trusting the new key.
- **Revoked connections.** When a connection becomes `revoking` or `revoked`, stop sending and close its runtime. Keep the state; do not reset keys.

## Security checklist <a href="#security-checklist" id="security-checklist"></a>

- Keep the state key in a secret store with mode `0600`, and the state directories at mode `0700`.
- Do not log decrypted messages, tokens or the state key.
- Do not copy endpoint state between servers while one is running.
- Publish your endpoint fingerprint outside ARX so account owners can compare it. `business_endpoint.get` returns it in `fingerprint`.
- List every service that receives decrypted messages as a data processor in [Advanced modes](../managed-bots.md#declare-your-operator-and-processors).

## Related pages <a href="#related-pages" id="related-pages"></a>

- [Business connections](business-connections.md) describes permissions, statuses and errors.
- [Polling and webhooks](../updates.md) covers offsets and update filters.
- [Security](../security.md) covers token storage.
