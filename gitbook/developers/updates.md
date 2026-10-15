---
description: "Configure update filters, poll safely and verify webhook deliveries with the SDKs."
---

# Polling and webhooks

You can receive your bot's updates through polling or a signed HTTPS webhook. Choose one delivery mode at a time, and process or durably save each update before acknowledging it. [Receiving updates](receiving-updates.md) helps you choose the transport; this guide covers its settings and delivery rules.

## Update fields <a href="#update-fields" id="update-fields"></a>

Each update has a decimal string `update_id`, a `type`, a `created_at` timestamp and a payload named after its type. IDs increase for each bot and can have gaps. Preserve them as strings; use integer arithmetic only when computing a polling offset.

| Update type | What your bot receives |
| --- | --- |
| `message` | A new content or service message. |
| `edited_message` | A changed message. |
| `my_chat_member` | A change to your bot's membership or access. |
| `callback_query` | A press on an inline callback button. |
| `poll` | Aggregate poll state. |
| `poll_answer` | An individual answer to a non-anonymous poll. |
| `stopped_message_generation` | A user's request to stop a draft generation. |
| `messages_deleted` | Deleted message IDs, when you opt in. |
| `message_reaction` | An individual reaction change, when you opt in. |
| `updates_dropped` | A count and reason for updates that could not be delivered. |

Other filter names are accepted, but their features may not send updates yet. Check [Not available yet](not-available-yet.md) for feature availability. Retain unknown variants and ignore unfamiliar fields so that a new field does not stop your bot.

## Filters <a href="#filters" id="filters"></a>

`allowed_updates` is a saved filter shared by polling and webhooks. Omitting it keeps the current filter. An empty polling string or webhook array restores the default set. An explicit list selects those types; include `default` to keep defaults and add opt-in types.

The four opt-in names are `messages_deleted`, `message_reaction`, `chat_member` and `message_reaction_count`. `stopped_message_generation` is already in the default set. `updates_dropped` always passes the filter, including when you select only one other type.

For example, use `default,messages_deleted,message_reaction` in the polling query or `["default","messages_deleted","message_reaction"]` in `webhook.set`.

The complete default set is:

```text
message, edited_message, channel_post, edited_channel_post, callback_query,
inline_query, chosen_inline_result, shipping_query, pre_checkout_query,
purchased_paid_media, subscription_updated, refund_request_updated,
login_grant_revoked, poll, poll_answer, my_chat_member, chat_join_request,
stopped_message_generation, managed_bot, guest_message, business_connection,
business_envelope, business_transfer_updated, gift_purchase_updated,
chat_boost, removed_chat_boost
```

A filter change affects only new events; queued updates follow the old filter. Polling accepts a comma-separated string with at most 2,048 bytes. Do not add spaces around names.

## Polling <a href="#polling" id="polling"></a>

Use one active poller per bot. Set `ARX_BOT_TOKEN` and run this example to process and acknowledge one batch. Its handler prints update IDs and types; replace that handler with your processing or durable save before using it for application work.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, Polling } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
if (!token) throw new Error("Set ARX_BOT_TOKEN");
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
const polling = new Polling(bot);
try {
	await polling.step(async (update) => {
		console.log(update.update_id, update.type);
	});
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
from arx_nexo import Client, Config, Update
from arx_nexo.polling import Polling

def handle(update: Update) -> None:
    print(update.update_id, update.type)

with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    polling = Polling(bot)
    try:
        polling.step(handle)
    finally:
        polling.close()
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, polling::Polling};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let mut polling = Polling::new(bot);
    let result = polling
        .step(
            |update| async move {
                println!("{} {}", update.update_id(), update.kind());
                Ok::<(), std::io::Error>(())
            },
            30,
        )
        .await;
    polling.close().await?;
    result?;
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The `Polling` helpers advance the offset only after all earlier updates in the batch have finished successfully. If a handler fails, they keep that update available for another delivery even if later work has finished. Their default deduplication stores live in memory. For restart safety, supply a persistent `DedupeStore`, save `PollingState.offset`, and restore both before polling again. Python also provides `AsyncPolling`; TypeScript and Rust process independent chats concurrently.

If you implement polling yourself, call `updates.get`, process `result.updates` in ascending ID order, then set `offset` to one more than the last completed ID. Send that offset with the next request. Use `BigInt` in TypeScript, `int` in Python or a checked integer in Rust. An empty batch after a timeout is normal.

| Query field | Values |
| --- | --- |
| `offset` | A decimal string that acknowledges all earlier update IDs. A negative `-N` discards all but the newest N pending updates. |
| `limit` | 1 to 100, with a default of 100. The response byte limit can shorten a batch. |
| `timeout_seconds` | 0 to 50 seconds. The API defaults to 0; the SDK polling helpers use 30. Keep the HTTP deadline longer than the poll timeout. |
| `allowed_updates` | A comma-separated [filter](#filters). |

A competing poller receives HTTP 409 `polling_conflict`. Polling with a configured webhook returns `webhook_active`; paused delivery returns `delivery_paused`. Use negative offsets only when you intend to discard older updates. [The updates reference](reference/updates.md#operation-updates-get) describes the typed request methods.

## Configure a webhook <a href="#configure-a-webhook" id="configure-a-webhook"></a>

Deploy a public HTTPS receiver that verifies requests, then call `webhook.set`. Set `ARX_BOT_TOKEN`, `REQUEST_KEY`, `WEBHOOK_URL` and `WEBHOOK_SECRET`. Use your deployed receiver URL and the same webhook secret in both the configuration and the receiver.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const webhookUrl = process.env.WEBHOOK_URL;
const secretToken = process.env.WEBHOOK_SECRET;
if (!token || !key || !webhookUrl || !secretToken) {
	throw new Error(
		"Set ARX_BOT_TOKEN, REQUEST_KEY, WEBHOOK_URL, WEBHOOK_SECRET",
	);
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const result = await bot.webhookSet({
		key,
		body: {
			url: webhookUrl,
			secret_token: secretToken,
			max_connections: 40,
			allowed_updates: ["default", "messages_deleted"],
			drop_pending_updates: false,
			reply_operations_enabled: false,
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

request = models.SetWebhook.model_validate(
    {
        "url": os.environ["WEBHOOK_URL"],
        "secret_token": os.environ["WEBHOOK_SECRET"],
        "max_connections": 40,
        "allowed_updates": ["default", "messages_deleted"],
        "drop_pending_updates": False,
        "reply_operations_enabled": False,
    }
)
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    result = bot.webhook_set(
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
    print(result)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let webhook_url = std::env::var("WEBHOOK_URL")?;
    let secret = std::env::var("WEBHOOK_SECRET")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let request: models::SetWebhook = serde_json::from_value(serde_json::json!({
      "url": webhook_url,
      "secret_token": secret,
      "max_connections": 40,
      "allowed_updates": [
        "default",
        "messages_deleted"
      ],
      "drop_pending_updates": false,
      "reply_operations_enabled": false
    }))?;
    let result = bot.webhook_set(&[], Some(&request), Some(&key)).await?;
    println!("{}", serde_json::to_string(&result)?);
    Ok(())
}
```

{% endtab %}
{% endtabs %}

| Setting | Requirement |
| --- | --- |
| `url` | Required. Use HTTPS on port 443, 80, 88 or 8443, with at most 2,048 UTF-8 bytes and no credentials or fragment. Every resolved address must be public. ARX does not follow redirects. |
| `ip_address` | Optional. Select a public IPv4 or IPv6 destination address. The URL still supplies the hostname for TLS verification. |
| `max_connections` | Use 1 to 100 simultaneous delivery requests. The default is 40. |
| `secret_token` | Optional. Use 1 to 256 ASCII letters, digits, underscores or hyphens. ARX sends it in `ARX-Bot-Api-Secret-Token`. |
| `allowed_updates` | Omit it to keep the saved filter, or provide the [filter array](#filters). |
| `drop_pending_updates` | Defaults to false. True intentionally discards the current backlog when you set the webhook. |
| `reply_operations_enabled` | Defaults to false. Enables [operations in the webhook response](#operations-in-a-webhook-response). |
| `certificate` | Optional. Use `{"attach":"certificate"}` and a matching multipart PEM part. Provide one X.509 certificate of at most 16 KiB. Its hostname must match the receiver, and it becomes the trust anchor for that receiver. |

The SDKs expose `webhookSetMultipart` or `webhook_set_multipart` for a custom certificate; the [webhook reference](reference/updates.md#operation-webhook-set) shows the multipart request. Configuration can succeed while your receiver is unreachable, so call `webhook.test` and inspect `webhook.get` before relying on delivery.

## Verify webhook deliveries <a href="#verify-webhook-deliveries" id="verify-webhook-deliveries"></a>

ARX sends one raw JSON update per POST with these headers:

```http
Content-Type: application/json
User-Agent: ARX-BotWebhook/1
ARX-Bot-Id: BOT_ID
ARX-Signature: t=TIMESTAMP,v1=HEX_SIGNATURE
ARX-Webhook-Delivery-Id: ATTEMPT_UUID
ARX-Webhook-Attempt: 1
ARX-Bot-Api-Secret-Token: CONFIGURED_SECRET
```

The secret header is present when you configure `secret_token`. Verify the raw body bytes before parsing or reserializing them. The SDK helpers check the signature, a timestamp tolerance of 300 seconds, and your configured secret. Set `ARX_BOT_TOKEN` and `WEBHOOK_SECRET` for these receiver functions:

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { WebhookVerifier } from "@arx/nexo";

export function verifyDelivery(
	rawBody: Uint8Array,
	headers: Readonly<Record<string, string>>,
) {
	const token = process.env.ARX_BOT_TOKEN;
	const secretToken = process.env.WEBHOOK_SECRET;
	if (!token || !secretToken)
		throw new Error("Set ARX_BOT_TOKEN and WEBHOOK_SECRET");
	return new WebhookVerifier(token, { secretToken }).verifyDelivery(
		rawBody,
		headers,
	);
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from collections.abc import Mapping
from arx_nexo import Update, WebhookVerifier

def verify_delivery(raw_body: bytes, headers: Mapping[str, str]) -> Update | None:
    verifier = WebhookVerifier.from_token(
        os.environ["ARX_BOT_TOKEN"],
        secret_token=os.environ["WEBHOOK_SECRET"],
    )
    return verifier.verify_delivery(raw_body, headers)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{models::Update, webhooks::WebhookVerifier};

pub fn verify_delivery(
    raw_body: &[u8],
    signature: &str,
    secret_header: Option<&str>,
) -> Result<Option<Update>, Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let secret = std::env::var("WEBHOOK_SECRET")?;
    let verifier = WebhookVerifier::new(&token, Some(&secret))?;
    Ok(verifier.verify_delivery(raw_body, signature, secret_header, None)?)
}
```

{% endtab %}
{% endtabs %}

Pass `ARX-Signature` and `ARX-Bot-Api-Secret-Token` to the Rust function's header arguments. The TypeScript and Python helpers accept a header mapping. Limit the body before buffering it, and return an error if verification fails.

A signed test ping has `{"type":"ping","ping":{"nonce":"..."}}` and no `update_id`. `verifyDelivery` or `verify_delivery` returns no update for it. Answer a valid ping with 2xx without inserting it in your update deduplication store.

For a complete receiver, open Webhooks in [TypeScript](https://github.com/unusnullus/arx-sdks/tree/main/examples/typescript/24%20-%20Webhooks), [Python](https://github.com/unusnullus/arx-sdks/tree/main/examples/python/24%20-%20Webhooks) or [Rust](https://github.com/unusnullus/arx-sdks/tree/main/examples/rust/24%20-%20Webhooks).

For framework integration, use the SDK adapters: Express, Fastify or Hono in TypeScript; FastAPI or aiohttp in Python; Axum in Rust. Their receiver helpers verify each request and run your handler before acknowledging the update. Keep the raw request body available to them.

After rotating a token, update your verifier to use the new complete token. See [Security](security.md#rotate-a-token) for the signing-key grace period and token management.

## Acknowledgements and retries <a href="#acknowledgements-and-retries" id="acknowledgements-and-retries"></a>

Return 2xx only after processing the update or saving it durably. An update held only in memory can be lost after a successful response and a crash.

| Receiver result | What ARX does |
| --- | --- |
| 2xx before the response deadline | Acknowledges the update. |
| Non-2xx with `Retry-After` | Waits for the requested delay, bounded to 1 second through 1 hour. The header can contain seconds or an HTTP date. |
| 3xx | Fails the attempt without following the redirect. |
| Other error or timeout | Retries until the update expires. |
| Every attempt returns 410 for more than 23 hours | Removes the webhook, preserves pending updates for polling and alerts the bot owner. |

The response deadline is 30 seconds, with 5-second connection and TLS deadlines. Ordinary retries use `min(2^(attempt - 1), 300)` seconds with a random multiplier from 0.5 to 1.5. Callback and Stop deliveries retry every second for their first 15 seconds, then follow that backoff.

If more than 95% of attempts fail over five minutes, delivery can pause with `circuit_open`. ARX probes every 30 seconds until an attempt succeeds. Other pause reasons are `token_revoked`, `owner_paused` and `bot_suspended`.

## Order and duplicates <a href="#order-and-duplicates" id="order-and-duplicates"></a>

Updates arrive at least once. Store processed update IDs durably, ideally in the same transaction as your application change. Do not deduplicate by the highest ID alone: concurrent work can complete out of order.

Polling returns ascending IDs. Webhook messages for the same chat are ordered, but different chats can arrive concurrently. Callbacks and Stop can pass ordinary messages waiting for a retry, so do not rely on one global delivery order.

`ARX-Webhook-Delivery-Id` identifies one delivery attempt and changes on retry. Use `update_id` to recognize the same update across attempts.

## Retention and drop reports <a href="#retention-and-drop-reports" id="retention-and-drop-reports"></a>

Updates expire after 24 hours. A bot can retain 1,000,000 updates by default. When the backlog exceeds that limit, ARX drops the oldest unacknowledged updates.

An `updates_dropped` update contains a count and a reason: `expired`, `backlog_limit`, `access_revoked` or `source_gap`. It does not contain the lost message content. Negative offsets and `drop_pending_updates` discard updates intentionally and produce no drop report.

After a bot transfer, the new bot owner does not receive updates queued for the previous bot owner. Deleted accounts can also cause queued data to be removed.

## Switch modes and inspect delivery <a href="#switch-modes-and-inspect-delivery" id="switch-modes-and-inspect-delivery"></a>

| Method | Behavior |
| --- | --- |
| `webhook.set` | Switches to webhooks. It preserves queued updates unless you set `drop_pending_updates`. An active poll ends with `webhook_active`. |
| `webhook.delete` | Returns to polling. Its `drop_pending_updates` query defaults to false. A request already in flight can still reach the old receiver. |
| `webhook.test` | Sends a signed test ping without consuming a queued update. |
| `webhook.get` | Returns delivery mode, backlog count and age, pause reason and sanitized recent errors. It does not return secrets or certificate bytes. |

Inspect `pending_update_count`, `oldest_pending_at`, `dropped_update_count_24h`, `last_error_*` and `last_reply_error_*` when diagnosing delivery. `platform.get` returns the current webhook sender networks in `webhook_egress_cidrs`. The bot owner can pause or resume delivery in [NexoCreator](nexocreator.md).

## Operations in a webhook response <a href="#operations-in-a-webhook-response" id="operations-in-a-webhook-response"></a>

If you enable `reply_operations_enabled`, a successful JSON response can request an operation:

```json
{
  "operation": "messages.send",
  "args": {
    "chat_id": "4398046511200",
    "content": {"type":"text","text":"Saved."}
  }
}
```

ARX executes it asynchronously with idempotency key `wh-<update_id>`. Use the SDK's `replyOperation` or `reply_operation` helper to construct this response. Call the API directly when you need the result.

The response operation cannot use multipart, `webhook.*` or `token.*`; read operations are ignored. A 2xx response still acknowledges the update when its body is invalid or exceeds 1 MiB. ARX skips that operation and records the failure in `last_reply_error_*`.

## User language <a href="#user-language" id="user-language"></a>

`User.language_code` is optional and uses a BCP 47 tag such as `en` or `pt-BR`. When available, it reports the language of the user's ARX app.

Choose a reply language from the update, then fall back to your stored user preference and the bot's default. [Commands & profile](commands-and-profile.md) explains their separate localization rules. For a complete update handler, open Echo Bot in [TypeScript](https://github.com/unusnullus/arx-sdks/tree/main/examples/typescript/22%20-%20Echo%20Bot), [Python](https://github.com/unusnullus/arx-sdks/tree/main/examples/python/22%20-%20Echo%20Bot) or [Rust](https://github.com/unusnullus/arx-sdks/tree/main/examples/rust/22%20-%20Echo%20Bot).
