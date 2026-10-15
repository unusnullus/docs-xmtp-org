# Updates and webhooks

Receive updates through polling or configure a webhook and inspect its delivery status.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`updates.get`](#operation-updates-get) | `GET /updates` |
| [`webhook.delete`](#operation-webhook-delete) | `DELETE /webhook` |
| [`webhook.get`](#operation-webhook-get) | `GET /webhook` |
| [`webhook.set`](#operation-webhook-set) | `PUT /webhook` |
| [`webhook.test`](#operation-webhook-test) | `POST /webhook/test` |

## updates.get <a href="#operation-updates-get" id="operation-updates-get"></a>

`GET /bot-api/v1/updates`

Receive updates through polling and acknowledge updates that you have processed.

Use one active polling consumer per bot. After successfully processing or durably saving an update, request an offset equal to its update ID plus one. Keep the offset as a decimal string. A negative offset intentionally discards older updates; use it only when you want that behavior. See [Updates](../updates.md) for saved filters, opt-in update types, and SDK polling helpers.

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `offset` | query | string | No | Pass the last processed update_id plus one to acknowledge earlier visible updates. A negative value -N keeps only the last N queued updates. The value must fit in a signed 64-bit integer. The value must match `^-?[0-9]+$`. |
| `limit` | query | integer | No | Set the maximum number of results to return. A response may contain fewer results. The minimum is 1. The maximum is 100. The default is `100`. |
| `timeout_seconds` | query | integer | No | Set the maximum time to wait for updates, in seconds. Zero returns immediately. The maximum is 50 seconds. The minimum is 0. The maximum is 50. The default is `0`. |
| `allowed_updates` | query | string | No | Use a comma-separated list of update types. Omit it to keep the saved filter, or use an empty value to restore the default filter. The update types currently delivered are message, edited_message, callback_query, poll, poll_answer, my_chat_member, stopped_message_generation, messages_deleted, message_reaction, and updates_dropped. messages_deleted and message_reaction require opt-in. chat_member and message_reaction_count also require opt-in but do not currently produce updates. Other accepted update type names do not currently produce events. The value default selects the default set alongside any explicitly selected types. You always receive updates_dropped, regardless of the filter. The maximum length is 2,048 characters. |

### Request <a href="#request" id="request"></a>

Do not send a request body.

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [UpdateBatch](types/updatebatch.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

HTTP 409 `polling_conflict` means another poll is active. HTTP 409 `webhook_active` means you must remove the webhook before polling. HTTP 409 `delivery_paused` means delivery needs to resume before polling can continue.

### Example <a href="#example" id="example"></a>

Set `ARX_BOT_TOKEN` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
if (!arxBotToken) {
  throw new Error("Set ARX_BOT_TOKEN");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.updatesGet({ query: {
    "timeout_seconds": 30,
    "limit": 100
  } });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.updates_get(timeout_seconds=30, limit=100)
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [("timeout_seconds", "30"), ("limit", "100")];
    let result = bot.updates_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 55 -X GET \
  "https://signal.arx.pro/bot-api/v1/updates?timeout_seconds=30&limit=100" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "updates": [
      {
        "created_at": "2026-10-11T12:00:00Z",
        "message": {
          "chat": {
            "id": "4398046511200",
            "kind": "private"
          },
          "content": {
            "entities": [],
            "text": "Hello",
            "type": "text"
          },
          "fallback_text": "Hello",
          "has_protected_content": false,
          "kind": "content",
          "message_id": "42",
          "revision": "1",
          "sender_user": {
            "id": "10000043",
            "is_bot": false,
            "name": "Ana López",
            "username": "ANALOPEZ"
          },
          "sent_at": "2026-10-11T12:00:00Z",
          "silent": false,
          "state_version": "42"
        },
        "type": "message",
        "update_id": "10"
      }
    ]
  }
}
```

## webhook.delete <a href="#operation-webhook-delete" id="operation-webhook-delete"></a>

`DELETE /bot-api/v1/webhook`

Remove the webhook so your bot can receive updates through polling.

Queued updates remain unless you set `drop_pending_updates: true`. Requests already sent to the previous endpoint can still arrive, so keep handling duplicates during the switch. Continue with [Updates](../updates.md) for polling offsets and delivery transitions.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `drop_pending_updates` | query | boolean | No | Set to true to discard queued updates when removing the webhook. The default is `false`. |

### Request <a href="#request-1" id="request-1"></a>

Do not send a request body.

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 409 `idempotency_key_reused` means the same key was used for different webhook deletion parameters. Use the original request to retry that action.

### Example <a href="#example-1" id="example-1"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.webhookDelete({ key: requestKey });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.webhook_delete(key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.webhook_delete(&query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X DELETE \
  "https://signal.arx.pro/bot-api/v1/webhook" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": true
}
```

## webhook.get <a href="#operation-webhook-get" id="operation-webhook-get"></a>

`GET /bot-api/v1/webhook`

Inspect your bot's update delivery mode, webhook settings, backlog, and recent delivery errors.

Treat the configured URL as sensitive because it can identify your application. Use the status and backlog fields to diagnose delivery without assuming that an empty backlog proves your handler processed every update. Continue with [Updates](../updates.md) for delivery acknowledgements and verification.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-2" id="request-2"></a>

Do not send a request body.

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [WebhookInfo](types/webhookinfo.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 400 `invalid_parameter` means the status request included an unsupported query or body. Send this GET without either.

### Example <a href="#example-2" id="example-2"></a>

Set `ARX_BOT_TOKEN` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
if (!arxBotToken) {
  throw new Error("Set ARX_BOT_TOKEN");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.webhookGet();
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.webhook_get()
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.webhook_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/webhook" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "allowed_updates": [],
    "dropped_update_count_24h": 0,
    "has_custom_certificate": false,
    "ip_address": null,
    "max_connections": 40,
    "mode": "polling",
    "oldest_pending_at": null,
    "pending_update_count": 0,
    "reply_operations_enabled": false,
    "url": null,
    "delivery_epoch": "1",
    "signing_key_generation": "1"
  }
}
```

## webhook.set <a href="#operation-webhook-set" id="operation-webhook-set"></a>

`PUT /bot-api/v1/webhook`

Configure signed POST delivery of updates to your HTTPS endpoint.

Use a public HTTPS URL and handle signature verification before processing each update. You can set concurrency, the saved update filter, a fixed IP address, and whether to discard queued updates. Queued updates remain unless you explicitly request their removal. Use JSON for ordinary settings, or multipart when attaching a certificate.

### Parameters <a href="#parameters-3" id="parameters-3"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-3" id="request-3"></a>

Content type: `application/json`. You must include the body.

[SetWebhook](types/setwebhook.md)

The HTTPS endpoint, verification settings, and update filter for webhook delivery. Use a public HTTPS endpoint on port 443, 80, 88, or 8443. The URL cannot contain credentials or a fragment, and redirects are not followed. The hostname must resolve to a public address. To use a custom certificate, send one PEM X.509 certificate of at most 16 KiB in a multipart request. It replaces the default certificate trust roots. Your endpoint does not have to be reachable when you save this configuration. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `allowed_updates` | Array of `"message"` or `"edited_message"` or `"channel_post"` or `"edited_channel_post"` or `"callback_query"` or `"inline_query"` or `"chosen_inline_result"` or `"shipping_query"` or `"pre_checkout_query"` or `"purchased_paid_media"` or `"subscription_updated"` or `"refund_request_updated"` or `"login_grant_revoked"` or `"poll"` or `"poll_answer"` or `"my_chat_member"` or `"chat_join_request"` or `"stopped_message_generation"` or `"managed_bot"` or `"guest_message"` or `"business_connection"` or `"business_envelope"` or `"business_transfer_updated"` or `"gift_purchase_updated"` or `"chat_boost"` or `"removed_chat_boost"` or `"default"` or `"updates_dropped"` or `"messages_deleted"` or `"chat_member"` or `"message_reaction"` or `"message_reaction_count"` | No | Select the update types to receive. Omit this field to keep the saved filter, or send an empty list to restore the default filter. The update types currently delivered are message, edited_message, callback_query, poll, poll_answer, my_chat_member, stopped_message_generation, messages_deleted, message_reaction, and updates_dropped. messages_deleted and message_reaction require opt-in. chat_member and message_reaction_count also require opt-in but do not currently produce updates. Other accepted update type names do not currently produce events. The value default selects the default set alongside any explicitly selected types. You always receive updates_dropped, regardless of the filter. Filter changes apply to new events. |
| `certificate` | object | No | Use this custom PEM X.509 certificate to verify your webhook server. Only the listed fields are allowed. |
| `certificate.attach` | string | Yes | This is the name of the binary part in the same multipart request. Use the part name without an attach:// prefix. The value must match `^[A-Za-z0-9_-]{1,64}$`. |
| `drop_pending_updates` | boolean | No | Set this to true to discard queued updates when applying this delivery change. The default is `false`. |
| `ip_address` | IPv4 or IPv6 address | No | A fixed public IPv4 or IPv6 address to connect to instead of resolving the webhook hostname. |
| `max_connections` | integer | No | This is the maximum number of simultaneous connections used to deliver webhook updates. The minimum is 1. The maximum is 100. The default is `40`. |
| `reply_operations_enabled` | boolean | No | This is true when your webhook may return an API operation in its response body. The default is `false`. |
| `secret_token` | string | No | The platform sends this secret in the Arx-Bot-Api-Secret-Token header on webhook deliveries. The value must match `^[A-Za-z0-9_-]{1,256}$`. |
| `url` | string | Yes | Set the public HTTPS endpoint that receives updates for your bot. The format is `uri`. The value must match `^https://`. The maximum length is 2,048 characters. |

Content type: `multipart/form-data`. You must include the body.

Send webhook settings in the JSON `payload` part. To supply a custom certificate, set `certificate.attach` to the name of its PEM X.509 binary part, at most 16 KiB. See [Polling and webhooks](../updates.md) for certificate and endpoint requirements. Let your HTTP library set the multipart boundary.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `payload` | [SetWebhook](types/setwebhook.md) | Yes | Send the JSON operation body in the payload part of a multipart request. |

### Response <a href="#response-3" id="response-3"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [WebhookInfo](types/webhookinfo.md) in `result` |

### Errors <a href="#errors-3" id="errors-3"></a>

HTTP 400 `invalid_parameter` can identify an invalid URL, address, certificate, or setting. HTTP 409 `delivery_mode_conflict` means you should check the active delivery mode before retrying. See [Updates](../updates.md) for the complete settings and handler requirements.

### Example <a href="#example-3" id="example-3"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY`, `WEBHOOK_URL` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
const webhookUrl = process.env.WEBHOOK_URL;
if (!arxBotToken || !requestKey || !webhookUrl) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, WEBHOOK_URL");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.SetWebhook = {
    "max_connections": 40,
    "url": webhookUrl
  };
  const result = await bot.webhookSet({ body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.SetWebhook(max_connections=40, url=os.environ["WEBHOOK_URL"])
    result = bot.webhook_set(body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let webhook_url = std::env::var("WEBHOOK_URL")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::SetWebhook {
        max_connections: Some(40),
        ..models::SetWebhook::new(webhook_url)
    };
    let query = [];
    let result = bot.webhook_set(&query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
payload=$(python3 - <<'PY'
import json
import os
print(json.dumps({'max_connections': 40, 'url': os.environ['WEBHOOK_URL']}))
PY
)

curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/webhook" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data "${payload}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "allowed_updates": [],
    "dropped_update_count_24h": 0,
    "has_custom_certificate": false,
    "ip_address": null,
    "max_connections": 40,
    "mode": "webhook",
    "oldest_pending_at": null,
    "pending_update_count": 0,
    "reply_operations_enabled": false,
    "url": "https://example.com/updates",
    "delivery_epoch": "1",
    "signing_key_generation": "1"
  }
}
```

## webhook.test <a href="#operation-webhook-test" id="operation-webhook-test"></a>

`POST /bot-api/v1/webhook/test`

Send a signed test ping to your configured webhook without consuming an update.

Send no body or `{}`. Handle the ping separately from updates because it has no update ID. The response reports the endpoint's status code and latency, with an error when delivery fails. You can run one test every ten seconds.

### Parameters <a href="#parameters-4" id="parameters-4"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-4" id="request-4"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-4" id="response-4"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [WebhookTest](types/webhooktest.md) in `result` |

### Errors <a href="#errors-4" id="errors-4"></a>

HTTP 400 `invalid_parameter` means you should check your webhook settings. See [Updates](../updates.md) for ping handling and signature verification.

### Example <a href="#example-4" id="example-4"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.webhookTest({ key: requestKey });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.webhook_test(key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.webhook_test(&query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 55 -X POST \
  "https://signal.arx.pro/bot-api/v1/webhook/test" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "latency_ms": 12,
    "status_code": 200
  }
}
```

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
