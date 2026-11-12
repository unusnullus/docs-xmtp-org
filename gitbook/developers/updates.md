---
description: "Configure update filters, poll safely and verify webhook deliveries with the SDKs."
---

# Polling and webhooks

You can receive your bot's updates through polling or a signed HTTPS webhook. Choose one delivery mode at a time, and process or durably save each update before acknowledging it. [Receiving updates](receiving-updates.md) helps you choose the transport; this guide covers its settings and delivery rules.

## Update fields <a href="#update-fields" id="update-fields"></a>

Each update has a decimal string `update_id`, a `type`, a `created_at` timestamp and one payload named after its type. IDs increase for each bot and can have gaps. Preserve them as strings; use integer arithmetic only when computing a polling offset.

```json
{
  "update_id": "10",
  "type": "message",
  "created_at": "2026-10-11T12:00:00Z",
  "message": {
    "message_id": "42",
    "chat": {"id": "123456789", "kind": "private"},
    "kind": "content",
    "content": {"type": "text", "text": "Hello", "entities": []},
    "sent_at": "2026-10-11T12:00:00Z"
  }
}
```

Branch on `type`, then read the payload with the same name. Use `chat.kind` (`private`, `group` or `channel`) to tell conversations apart. A message in a forum topic or a private topic carries `topic_id`. The [updates reference](reference/updates.md) lists every field.

## Update types <a href="#update-types" id="update-types"></a>

### Messages and conversations <a href="#messages-and-conversations" id="messages-and-conversations"></a>

| Update type | What your bot receives | Filter |
| --- | --- | --- |
| `message` | A new content or service message in a private chat or group. Topic events, such as a created or closed topic, arrive as service messages. | Default |
| `edited_message` | A message after an edit your bot can observe. | Default |
| `messages_deleted` | The IDs of deleted messages in one chat. | Opt-in |
| `message_reaction` | One user's reaction change on a message. | Opt-in |
| `channel_post` | A new post in a channel your bot belongs to. | Default |
| `edited_channel_post` | An edited channel post. It keeps the original send time. | Default |
| `callback_query` | A press on an inline callback button. Answer it with `callback_queries.answer`. | Default |
| `poll` | The aggregate state of a poll your bot sent. | Default |
| `poll_answer` | One user's vote or vote change in a non-anonymous poll. | Default |
| `stopped_message_generation` | A user stopped a draft your bot was generating. See [Drafts](drafts.md). | Default |

### Membership and groups <a href="#membership-and-groups" id="membership-and-groups"></a>

| Update type | What your bot receives | Filter |
| --- | --- | --- |
| `my_chat_member` | A change to your bot's own membership or access in a private chat, group or channel. In a private chat, `new.status` of `banned` means the user blocked your bot. | Default |
| `chat_member` | A change to another member's role or membership in a group. Your bot must be an administrator. | Opt-in |
| `chat_join_request` | A pending request to join a group. Your bot must be an administrator with the right to invite users. | Default |

`old` and `new` in a membership update describe the subject's membership before and after the change; `user` is the account that made the change. An update you already received remains valid after your bot leaves the group, but every later operation in that group needs current membership.

In groups, your bot receives messages, edits, deletions, membership changes, join requests and presses on its own buttons. Which group messages it receives depends on its group privacy setting (`/setprivacy` in NexoCreator) and administrator status. Receiving an update grants no additional group permission. [Groups](groups.md) explains how to add your bot to a group and which rights it needs.

When a `chat_join_request` carries `query_id`, your bot is the guard for that request and the update also has `expires_at`. Answer with `join_request_queries.answer` before that time. A request without `query_id` is a notice: approve or decline it later with `join_requests.approve` or `join_requests.decline`.

### Payments <a href="#payments" id="payments"></a>

| Update type | What your bot receives | Filter |
| --- | --- | --- |
| `pre_checkout_query` | A payment waiting for your approval. Answer before `expires_at`; the original deadline is ten seconds and retries do not extend it. | Default |
| `subscription_updated` | The current state of a subscription after a payment or lifecycle change. | Default |
| `renewal_due` | A reminder that a manually renewed subscription is due. The payload has `user`, `subscription_id`, `plan_id`, `current_period_end_at` and `grace_ends_at`. It does not mean anything was paid. See [Subscriptions and renewals](payments/subscriptions.md#renewal-reminders). | Opt-in |

A confirmed payment arrives as a `message` whose service content is `successful_payment`. Payment-related updates carry `pay_seq` and `prev_pay_seq`, which order the events for one payer and bot. If `prev_pay_seq` does not match the last sequence you stored for that payer, you missed an event: read the current state before acting. No dedicated update reports a refund. A refund that changes a subscription can emit `subscription_updated`; read the buyer's access, as [Refunds](payments/refunds.md) explains. [Payments](payments.md) explains the full flow.

### Bot management and connected accounts <a href="#bot-management-and-connected-accounts" id="bot-management-and-connected-accounts"></a>

| Update type | What your bot receives | Filter |
| --- | --- | --- |
| `managed_bot` | A change to a bot your bot manages, such as a rotated token or a new human owner. The payload has `bot`, its human owner in `user`, and `is_managed`. | Default |
| `business_connection` | A connected account's state or granted rights changed. It grants no permission to send a private message. | Default |
| `business_envelope` | An encrypted event from a connected account. Open it with the SDK's Business runtime. | Default |
| `inline_query` | An inline query to answer before its deadline. Inline mode is [not available yet](not-available-yet.md), so none arrive today. | Default |
| `guest_message` | A guest invocation assigned to your bot only. It grants no right to read history or join the source chat. | Default |

These updates arrive only when the matching feature is available to your bot. See [Managed bots](managed-bots/managed-bots.md) and [Business connections](managed-bots/business-connections.md), and check [Not available yet](not-available-yet.md) before you design around one.

### Delivery notices <a href="#delivery-notices" id="delivery-notices"></a>

| Update type | What your bot receives | Filter |
| --- | --- | --- |
| `updates_dropped` | A count and reason for updates that could not be delivered. See [Retention and drop reports](#retention-and-drop-reports). | Always delivered |

Retain unknown update types and ignore unfamiliar fields, so that a new type or field does not stop your worker. Acknowledge an update you do not handle; leaving it unacknowledged blocks polling progress.

## Filters <a href="#filters" id="filters"></a>

`allowed_updates` is a saved filter shared by polling and webhooks. Omitting it keeps the current filter. An empty polling string or webhook array restores the default set. An explicit list selects only those types; include `default` to keep the defaults and add opt-in types.

The opt-in types that produce updates today are `messages_deleted`, `message_reaction`, `chat_member` and `renewal_due`. The filter also accepts `message_reaction_count`, `business_message`, `edited_business_message` and `deleted_business_messages`, but the API sends no updates of these types. The SDK Business runtime derives `business_message`, `edited_business_message` and `deleted_business_messages` locally from `business_envelope` updates; see [Business endpoint](managed-bots/business-endpoint.md). `updates_dropped` always passes the filter, including when you select only one other type.

For example, use `default,messages_deleted,message_reaction` in the polling query or `["default","messages_deleted","message_reaction"]` in `webhook.set`. A group bot that tracks members uses `default,chat_member`.

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

Some names in the default set reserve a type for a later feature and send nothing today. An unknown name fails the request with HTTP 400. A filter change affects only new events; queued updates follow the old filter. Polling accepts a comma-separated string with at most 2,048 bytes. Do not add spaces around names.

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
const webhookSecret = process.env.WEBHOOK_SECRET;
if (!token || !key || !webhookUrl || !webhookSecret) {
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
			secret_token: webhookSecret,
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

webhook_url = os.environ["WEBHOOK_URL"]
webhook_secret = os.environ["WEBHOOK_SECRET"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.SetWebhook(
        url=webhook_url,
        secret_token=webhook_secret,
        max_connections=40,
        allowed_updates=["default", "messages_deleted"],
        drop_pending_updates=False,
        reply_operations_enabled=False,
    )
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
    let webhook_secret = std::env::var("WEBHOOK_SECRET")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let request = models::SetWebhook {
        allowed_updates: Some(vec![
            models::SetWebhookAllowedUpdatesItem::Default,
            models::SetWebhookAllowedUpdatesItem::MessagesDeleted,
        ]),
        drop_pending_updates: Some(false),
        max_connections: Some(40),
        reply_operations_enabled: Some(false),
        secret_token: Some(webhook_secret),
        ..models::SetWebhook::new(webhook_url)
    };
    let result = bot.webhook_set(&query, Some(&request), Some(&key)).await?;
    println!("{result:?}");
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

ARX sends one raw JSON update per POST. For example, a delivery can have these headers:

```http
Content-Type: application/json
User-Agent: ARX-BotWebhook/1
ARX-Bot-Id: 1001
ARX-Signature: t=1792069200,v1=9f46cf97deef68e101354ae2d472ff622ecad50cdc5dba1bf90e20b5a429d6d4
ARX-Webhook-Delivery-Id: 4a708670-01d2-4a57-a88c-f6cb4935c64e
ARX-Webhook-Attempt: 1
ARX-Bot-Api-Secret-Token: docs_example_secret_1
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

### Verify without an SDK <a href="#verify-without-an-sdk" id="verify-without-an-sdk"></a>

If your receiver is written in another language, implement the same checks. Use HMAC-SHA256 throughout.

1. Derive a master key: HMAC with the ASCII key `AERX-BOT-SIGNING-v1` over your complete bot token.
2. Derive the webhook key: HMAC with the master key over the ASCII bytes `aerx/webhook/v1` followed by one byte `0x01`.
3. Split `ARX-Signature` on commas. It contains exactly one `t=<unix seconds>` and one or more `v1=<64 hex digits>` entries.
4. Compute HMAC with the webhook key over `<t>.` followed by the raw body bytes. Accept the delivery if any `v1` value matches, compared in constant time.
5. Reject the delivery if `t` differs from your clock by more than 300 seconds, or if `ARX-Bot-Api-Secret-Token` does not equal your configured secret.

This Python function uses only the standard library:

```python
import hashlib
import hmac
import time

def verify_signature(token: str, raw_body: bytes, signature: str) -> bool:
    master = hmac.new(b"AERX-BOT-SIGNING-v1", token.encode(), hashlib.sha256).digest()
    key = hmac.new(master, b"aerx/webhook/v1\x01", hashlib.sha256).digest()
    parts = [part.strip().split("=", 1) for part in signature.split(",")]
    stamps = [value for name, value in parts if name == "t"]
    digests = [value for name, value in parts if name == "v1"]
    if len(stamps) != 1 or not stamps[0].isdigit() or not digests:
        return False
    if abs(time.time() - int(stamps[0])) > 300:
        return False
    expected = hmac.new(key, stamps[0].encode() + b"." + raw_body, hashlib.sha256).hexdigest()
    return any(hmac.compare_digest(expected, value.lower()) for value in digests)
```

Check your implementation against this test vector, with the timestamp check disabled:

| Input | Value |
| --- | --- |
| Token | `123456789:aerx_EXAMPLE_TOKEN_FOR_DOCS_ONLY_0000000000000000` |
| Raw body | `{"type":"ping","ping":{"nonce":"docs-example"}}` |
| `ARX-Signature` | `t=1792069200,v1=9f46cf97deef68e101354ae2d472ff622ecad50cdc5dba1bf90e20b5a429d6d4` |

During the grace period after a token rotation, the header carries one `v1` entry per valid key. Accept the delivery when any of them matches.

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

The response deadline is 30 seconds, with 5-second connection and TLS deadlines. Ordinary retries use `min(2^(attempt - 1), 300)` seconds with a random multiplier from 0.5 to 1.5. Callback and Stop deliveries retry every second for their first 15 seconds, then follow that backoff. A `pre_checkout_query` or a guard `chat_join_request` retries every second until its `expires_at` deadline; answer it as soon as it arrives.

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

Inspect `pending_update_count`, `oldest_pending_at`, `dropped_update_count_24h`, `last_error_*` and `last_reply_error_*` when diagnosing delivery. The bot owner can pause or resume delivery in [NexoCreator](nexocreator.md).

## Operations in a webhook response <a href="#operations-in-a-webhook-response" id="operations-in-a-webhook-response"></a>

If you enable `reply_operations_enabled`, a successful HTTP JSON response can request an operation:

```json
{
  "operation": "messages.send",
  "args": {
    "chat_id": "123456789",
    "content": {"type":"text","text":"Saved."}
  }
}
```

ARX executes it asynchronously with idempotency key `wh-<update_id>`. Use the SDK's `replyOperation` or `reply_operation` helper to construct this response. Call the API directly when you need the result.

The response operation cannot use multipart, `webhook.*` or `token.*`; read operations are ignored. A 2xx response still acknowledges the update when its body is invalid or exceeds 1 MiB. ARX skips that operation and records the failure in `last_reply_error_*`.

## User language <a href="#user-language" id="user-language"></a>

`User.language_code` is optional and uses a BCP 47 tag such as `en` or `pt-BR`. When available, it reports the language of the user's ARX app.

Choose a reply language from the update, then fall back to your stored user preference and the bot's default. [Commands & profile](commands-and-profile.md) explains their separate localization rules. For a complete update handler, open Echo Bot in [TypeScript](https://github.com/unusnullus/arx-sdks/tree/main/examples/typescript/22%20-%20Echo%20Bot), [Python](https://github.com/unusnullus/arx-sdks/tree/main/examples/python/22%20-%20Echo%20Bot) or [Rust](https://github.com/unusnullus/arx-sdks/tree/main/examples/rust/22%20-%20Echo%20Bot).
