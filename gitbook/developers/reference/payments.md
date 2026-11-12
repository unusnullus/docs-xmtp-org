# Payments

Create invoice links, answer pre-checkout queries, read plans, balances, and subscriptions, and refund charges.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`charges.refund`](#operation-charges-refund) | `POST /charges/{charge_id}/refund` |
| [`entitlements.get`](#operation-entitlements-get) | `GET /entitlements/{user_id}` |
| [`invoice_links.create`](#operation-invoice-links-create) | `POST /invoice_links` |
| [`merchant_balance.get`](#operation-merchant-balance-get) | `GET /merchant/balance` |
| [`payment_analytics.get`](#operation-payment-analytics-get) | `GET /payment_analytics` |
| [`payment_plans.list`](#operation-payment-plans-list) | `GET /payment_plans` |
| [`payment_readiness.get`](#operation-payment-readiness-get) | `GET /payment_readiness` |
| [`payment_tokens.list`](#operation-payment-tokens-list) | `GET /payment_tokens` |
| [`pre_checkout_queries.answer`](#operation-pre-checkout-queries-answer) | `POST /pre_checkout_queries/{query_id}/answer` |
| [`subscriptions.list`](#operation-subscriptions-list) | `GET /subscriptions` |
| [`subscriptions.get`](#operation-subscriptions-get) | `GET /subscriptions/{subscription_id}` |
| [`subscriptions.update`](#operation-subscriptions-update) | `PATCH /subscriptions/{subscription_id}` |
| [`subscriptions.extend`](#operation-subscriptions-extend) | `POST /subscriptions/{subscription_id}/extend` |

## charges.refund <a href="#operation-charges-refund" id="operation-charges-refund"></a>

`POST /bot-api/v1/charges/{charge_id}/refund`

Request a refund for a confirmed charge your bot received. Pass the `charge_id` from the `successful_payment` service message and the payer's `user_id`. Omit `amount` to refund the whole remaining amount, or send an exact base-unit string for a partial refund. The recipient, network and asset always come from the original charge.

A successful call returns HTTP 202 with `refund_id`, `state: "awaiting_merchant_transfer"` and `expires_at`. This is an accepted request, not a completed refund: the bot owner still has to send the return transfer from their linked main wallet in NexoCreator before it expires. There is no refund-progress update. After the owner sends the refund, read `entitlements.get` and `subscriptions.list` for the result. Send an `Idempotency-Key` header and reuse the same key and body on retry; a replay returns the original receipt.

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `charge_id` | path | string | Yes | The charge ID from a confirmed payment to this bot. The value must match `^[A-Za-z0-9_:-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |

### Request <a href="#request" id="request"></a>

Content type: `application/json`. You must include the body.

[RefundCharge](types/refundcharge.md)

A refund instruction for a confirmed charge. Recipient, network and asset come from the original charge. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `amount` | string | No | Exact positive base units to refund, no greater than uint256 maximum or the remaining refundable amount. Omit for the full remaining amount; do not send null. The value must match `^[1-9][0-9]*$`. The maximum length is 78 characters. |
| `user_id` | string | Yes | The decimal public ID, available to this bot, of the original payer of the charge. The value must match `^[1-9][0-9]*$`. The maximum length is 20 characters. |

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 202 | JSON envelope with [RefundRequest](types/refundrequest.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

HTTP 404 `charge_not_found` means the charge is not available to your bot; check the `charge_id`. HTTP 400 `refund_amount_exceeded` means the amount is more than the remaining refundable amount. HTTP 403 `access_restricted` means the buyer no longer has a chat with your bot. HTTP 409 `idempotency_key_reused` means the key belongs to another request. HTTP 400 `invalid_request` means a field is malformed or `amount` is null. HTTP 503 `upstream_unavailable` means the charge is not confirmed or a dependency is unavailable; retry with the same key and body. See [Payments](../payments.md).

### Example <a href="#example" id="example"></a>

Set `ARX_BOT_TOKEN`, `CHARGE_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chargeId = process.env.CHARGE_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chargeId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHARGE_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.chargesRefund(chargeId, { key: requestKey });
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
    result = bot.charges_refund(os.environ["CHARGE_ID"], key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let charge_id = std::env::var("CHARGE_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.charges_refund(&charge_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X POST \
  "https://signal.arx.pro/bot-api/v1/charges/${CHARGE_ID}/refund" \
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
    "expires_at": "2030-01-08T00:00:00Z",
    "refund_id": "refund-1",
    "state": "awaiting_merchant_transfer"
  }
}
```

## entitlements.get <a href="#operation-entitlements-get" id="operation-entitlements-get"></a>

`GET /bot-api/v1/entitlements/{user_id}`

Check whether a user currently has paid access to your bot. Use this verdict to gate paid features instead of your own record of a checkout answer or a pending transaction.

`has_access` is the decision. `source` says what supports it: `none`, `one_time` or `subscription`. `access_until` is the current access deadline, or `null` when no finite deadline applies; a `null` value can mean perpetual access, so always read `has_access` too. Read the entitlement again after a `subscription_updated` or `payment_reverted` update, because a lapsed renewal or a chain reorganization can remove access.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `user_id` | path | string | Yes | The decimal user ID available to this bot. The minimum length is 1 character. The maximum length is 128 characters. |

### Request <a href="#request-1" id="request-1"></a>

Do not send a request body.

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Entitlement](types/entitlement.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 404 `user_not_found` means the user ID is unknown or not available to your bot; use the decimal user ID, not a private chat ID. HTTP 503 `upstream_unavailable` means the verdict could not be read; do not grant access on this error. See [Payments](../payments.md).

### Example <a href="#example-1" id="example-1"></a>

Set `ARX_BOT_TOKEN`, `USER_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const userId = process.env.USER_ID;
if (!arxBotToken || !userId) {
  throw new Error("Set ARX_BOT_TOKEN, USER_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.entitlementsGet(userId);
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
    result = bot.entitlements_get(os.environ["USER_ID"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let user_id = std::env::var("USER_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.entitlements_get(&user_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X GET \
  "https://signal.arx.pro/bot-api/v1/entitlements/${USER_ID}" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "has_access": true,
    "source": "subscription",
    "access_until": "2026-12-11T12:00:00Z"
  }
}
```

## invoice_links.create <a href="#operation-invoice-links-create" id="operation-invoice-links-create"></a>

`POST /bot-api/v1/invoice_links`

Create a payment link that you can share outside a single chat, for example in a button or your Mini App. The buyer pays from the in-app wallet with an ordinary transfer to the bot owner's payout wallet. The response contains the link `url`, its `slug` and the `invoice_id`.

Choose `asset` from the tokens returned by `payment_tokens.list` and send `amount` as an exact base-unit string. `title` takes up to 32 characters, `description` up to 255, and `payload` up to 128 bytes; keep your order reference in `payload`. Add `plan_id` to sell a plan returned by `payment_plans.list`. Send an `Idempotency-Key` header and reuse the same key and body when retrying. Each purchase through the link still sends your bot a pre-checkout query to answer.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-2" id="request-2"></a>

Content type: `application/json`. You must include the body.

[CreateInvoiceLink](types/createinvoicelink.md)

Create a payment link using a configured asset and an exact base-unit amount.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `amount` | string | Yes | The exact amount in asset base units, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. The maximum length is 78 characters. |
| `asset` | string | Yes | The payment asset identifier, including its network and token contract or native asset. The minimum length is 1 character. The maximum length is 256 characters. |
| `description` | string | Yes | This is the description displayed for the object. The minimum length is 1 character. The maximum length is 255 characters. |
| `payload` | string | Yes | Send the JSON operation body in the payload part of a multipart request. The minimum length is 1 character. The maximum length is 128 characters. The limit is 128 UTF-8 bytes. |
| `plan_id` | string | No | The configured payment plan linked to this invoice, when present. The minimum length is 1 character. The maximum length is 128 characters. |
| `title` | string | Yes | This is the title displayed for the object. The minimum length is 1 character. The maximum length is 32 characters. |

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [InvoiceLink](types/invoicelink.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 400 `invalid_request` means a field is missing, too long or not allowed, the asset is not accepted, the amount is outside its limits, or `plan_id` is unknown or differs from `asset`/`amount`. HTTP 409 `merchant_not_ready` means payment setup is incomplete; check `payment_readiness.get`. HTTP 409 `idempotency_key_reused` means the key belongs to another request; retry with its original body or use a new key. HTTP 503 `upstream_unavailable` means a dependency is temporarily unavailable; retry with the same key. See [Payments](../payments.md).

### Example <a href="#example-2" id="example-2"></a>

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
  const result = await bot.invoiceLinksCreate({ key: requestKey });
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
    result = bot.invoice_links_create(key=os.environ["REQUEST_KEY"])
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
    let result = bot.invoice_links_create(&query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X POST \
  "https://signal.arx.pro/bot-api/v1/invoice_links" \
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
    "invoice_id": "invoice-1",
    "slug": "link-1",
    "url": "https://pay.example.test/link-1"
  }
}
```

## merchant_balance.get <a href="#operation-merchant-balance-get" id="operation-merchant-balance-get"></a>

`GET /bot-api/v1/merchant/balance`

Read the current holdings of the payout wallet that receives your bot's payments. Each entry in `balances` has a `chain`, an `asset`, an exact base-unit `amount` and `decimals`.

Balances are observed on chain within the last 30 seconds. They show what the wallet holds, which can include money unrelated to your bot, so do not use them to confirm an invoice or decide access. Keep each network and asset separate and keep amounts as strings; a zero balance is `"0"`, and a missing observation is left out rather than reported as zero. The operation takes no parameters.

### Parameters <a href="#parameters-3" id="parameters-3"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-3" id="request-3"></a>

Do not send a request body.

### Response <a href="#response-3" id="response-3"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [MerchantBalance](types/merchantbalance.md) in `result` |

### Errors <a href="#errors-3" id="errors-3"></a>

HTTP 400 `invalid_request` means the request carried a query string or body. HTTP 503 `upstream_unavailable` means a fresh observation was not available; wait and retry, and do not treat it as an empty wallet. See [Payments](../payments.md).

### Example <a href="#example-3" id="example-3"></a>

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
  const result = await bot.merchantBalanceGet();
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
    result = bot.merchant_balance_get()
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
    let result = bot.merchant_balance_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X GET \
  "https://signal.arx.pro/bot-api/v1/merchant/balance" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "balances": []
  }
}
```

## payment_analytics.get <a href="#operation-payment-analytics-get" id="operation-payment-analytics-get"></a>

`GET /bot-api/v1/payment_analytics`

Payment analytics are not available yet. Every call returns HTTP 503 `upstream_unavailable`; this is not an empty report.

The operation will read daily payment totals for your bot over a date range. Each bucket has a `date`, `revenue` per asset in base units, subscription counters such as `new`, `renewals`, `churned` and `active_at_end`, and the nullable decimal strings `revenue_usd_cents`, `mrr_usd_cents` and `avg_tenure_days`.

`from` is inclusive and `to` is exclusive, both UTC dates in `YYYY-MM-DD` form; the range spans 1 to 366 days. Only `granularity=day` is accepted. Pass `plan_id` to report one plan. Buckets are in ascending date order and days without data are left out. A `null` metric is unknown, not zero. Keep amounts as strings and never add different assets together. Read the report again to see corrections after a chain reorganization.

### Parameters <a href="#parameters-4" id="parameters-4"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `from` | query | string | Yes | Inclusive UTC calendar date, YYYY-MM-DD, from 1970 onward. The format is `date`. |
| `to` | query | string | Yes | Exclusive UTC calendar date, YYYY-MM-DD, at most 366 days after from. The format is `date`. |
| `granularity` | query | `"day"` | No | Calendar-day aggregation in UTC. The default is `"day"`. |
| `plan_id` | query | string | No | Restrict the report to a payment plan owned by this bot. The value must match `^[A-Za-z0-9_:-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |

### Request <a href="#request-4" id="request-4"></a>

Do not send a request body.

### Response <a href="#response-4" id="response-4"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [PaymentAnalytics](types/paymentanalytics.md) in `result` |

### Errors <a href="#errors-4" id="errors-4"></a>

HTTP 400 `invalid_request` means a date is malformed, the range is empty or longer than 366 days, or another parameter is invalid. HTTP 503 `upstream_unavailable` is returned until analytics are available. See [Payments](../payments.md).

### Example <a href="#example-4" id="example-4"></a>

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
  const result = await bot.paymentAnalyticsGet();
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
    result = bot.payment_analytics_get()
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
    let result = bot.payment_analytics_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X GET \
  "https://signal.arx.pro/bot-api/v1/payment_analytics" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "buckets": [],
    "from": "2026-01-01",
    "granularity": "day",
    "plan_id": null,
    "to": "2026-01-02"
  }
}
```

## payment_plans.list <a href="#operation-payment-plans-list" id="operation-payment-plans-list"></a>

`GET /bot-api/v1/payment_plans`

List the payment plans your bot's owner configured. Use a plan's `plan_id` when you create an invoice link for it, and show its terms to buyers.

Each item is a `one_time` or `recurring` plan with a terms `version` and a `token` holding the exact base-unit `amount` and `decimals`. For `recurring` plans, `period_seconds` is the paid period and `grace_seconds` the grace period after it; both are zero for `one_time`. Subscriptions renew manually, so each renewal needs a new wallet approval from the buyer. Pass `limit` (1 to 100, default 20) and send `next_cursor` back as `cursor` until it is `null`. Listing plans creates no purchase.

### Parameters <a href="#parameters-5" id="parameters-5"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `cursor` | query | string | No | Opaque cursor returned by the payment authority for this bot. The value must match `^[!-~]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `limit` | query | integer | No | Maximum number of plans to return, from 1 to 100. The minimum is 1. The maximum is 100. The default is `20`. |

### Request <a href="#request-5" id="request-5"></a>

Do not send a request body.

### Response <a href="#response-5" id="response-5"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [PaymentPlanPage](types/paymentplanpage.md) in `result` |

### Errors <a href="#errors-5" id="errors-5"></a>

HTTP 400 `invalid_request` means `limit` is out of range or `cursor` is malformed. HTTP 403 `capability_not_available` means plans are not available to your bot. HTTP 503 `upstream_unavailable` means the catalog could not be read; do not treat it as an empty list. See [Payments](../payments.md).

### Example <a href="#example-5" id="example-5"></a>

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
  const result = await bot.paymentPlansList();
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
    result = bot.payment_plans_list()
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
    let result = bot.payment_plans_list(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X GET \
  "https://signal.arx.pro/bot-api/v1/payment_plans" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "items": [],
    "next_cursor": null
  }
}
```

## payment_readiness.get <a href="#operation-payment-readiness-get" id="operation-payment-readiness-get"></a>

`GET /bot-api/v1/payment_readiness`

Check whether your bot's payment setup passes the checkout checks before you send invoices or invoice links. The response is a fresh setup verdict, not a payment confirmation or an access grant.

`ready: false` is a successful result. In that case `reason` is `payout_address_missing` or `checklist_incomplete`, and `missing` lists the items to complete, such as `terms`, `support_contact`, `delivery_mode`, `allowed_updates:pre_checkout_query` or `payout_address:<network>`. `delivery_mode` shows how your bot receives updates, and `payment_updates_allowed` shows which payment update types are enabled. Your bot must receive `pre_checkout_query` updates to take payments. The operation takes no parameters; read it again after you fix the setup.

### Parameters <a href="#parameters-6" id="parameters-6"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-6" id="request-6"></a>

Do not send a request body.

### Response <a href="#response-6" id="response-6"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [PaymentReadiness](types/paymentreadiness.md) in `result` |

### Errors <a href="#errors-6" id="errors-6"></a>

HTTP 400 `invalid_request` means the request carried a query string or body; send a plain `GET`. HTTP 503 `upstream_unavailable` means the setup could not be read; wait and retry, and do not treat it as a not-ready verdict. See [Payments](../payments.md) for the setup steps.

### Example <a href="#example-6" id="example-6"></a>

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
  const result = await bot.paymentReadinessGet();
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
    result = bot.payment_readiness_get()
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
    let result = bot.payment_readiness_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X GET \
  "https://signal.arx.pro/bot-api/v1/payment_readiness" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "delivery_mode": "polling",
    "missing": [],
    "payment_updates_allowed": {
      "pre_checkout_query": true,
      "purchased_paid_media": false,
      "shipping_query": false,
      "subscription_updated": true
    },
    "payout_addresses": [
      {
        "chain": "eip155:56",
        "registered": true,
        "verified": true
      }
    ],
    "polling_backlog": 0,
    "ready": true
  }
}
```

## payment_tokens.list <a href="#operation-payment-tokens-list" id="operation-payment-tokens-list"></a>

`GET /bot-api/v1/payment_tokens`

List the payment assets your bot can price invoices in. Each entry in `tokens` has a `chain`, an `asset` identifier, a display `symbol`, the token `contract` (`null` for a native currency), `decimals`, and the inclusive `min_amount` and `max_amount` in base units.

Select an asset by its `asset` identifier, never by `symbol`: the same symbol can exist on several networks. Keep amounts as strings and convert them with the returned `decimals`. The list is server configuration, so read it again for current limits rather than hard-coding it. Creating an invoice also checks which assets the bot's owner accepts, so an asset in this list can still be refused. The operation takes no parameters and creates no purchase.

### Parameters <a href="#parameters-7" id="parameters-7"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-7" id="request-7"></a>

Do not send a request body.

### Response <a href="#response-7" id="response-7"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [PaymentTokenCatalog](types/paymenttokencatalog.md) in `result` |

### Errors <a href="#errors-7" id="errors-7"></a>

HTTP 400 `invalid_request` means the request carried a query string or body. HTTP 503 `upstream_unavailable` means the catalog could not be read; wait and retry, and do not treat it as an empty list. See [Payments](../payments.md) for choosing an asset and amount.

### Example <a href="#example-7" id="example-7"></a>

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
  const result = await bot.paymentTokensList();
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
    result = bot.payment_tokens_list()
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
    let result = bot.payment_tokens_list(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X GET \
  "https://signal.arx.pro/bot-api/v1/payment_tokens" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "tokens": []
  }
}
```

## pre_checkout_queries.answer <a href="#operation-pre-checkout-queries-answer" id="operation-pre-checkout-queries-answer"></a>

`POST /bot-api/v1/pre_checkout_queries/{query_id}/answer`

Approve or reject a purchase after you receive a `pre_checkout_query` update. The buyer's payment does not proceed until you answer. Before approving, check the query's payload and exact amount against the order your bot stored.

Send `ok: true` to approve. To reject, send `ok: false` with an `error_message` of 1 to 256 characters; do not include `error_message` when approving. Each query can be answered once and expires at the `expires_at` it carries, so answer promptly. Approval is not proof of payment: deliver the purchase only after the confirmed `successful_payment` service message arrives. The response is `true`.

### Parameters <a href="#parameters-8" id="parameters-8"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `query_id` | path | string | Yes | The exact pre-checkout query ID received by this bot. The minimum length is 1 character. The maximum length is 128 characters. |

### Request <a href="#request-8" id="request-8"></a>

Content type: `application/json`. You must include the body.

[PreCheckoutAnswer](types/precheckoutanswer.md)

The approval or buyer-visible rejection of one pre-checkout query. Only the listed fields are allowed. If `ok` is omitted or is `false`, include `error_message`. If `ok` is omitted or is `true`, omit `error_message`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `error_message` | string | No | Plain-text reason shown to the buyer. Required when ok is false; omitted when ok is true. The minimum length is 1 character. The maximum length is 256 characters. |
| `ok` | boolean | Yes | Approve the payment when true; decline it when false. |

### Response <a href="#response-8" id="response-8"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-8" id="errors-8"></a>

HTTP 410 `query_expired` means the query was already answered, expired, is unknown or belongs to another bot; do not answer it again. HTTP 400 `invalid_request` means the body has extra fields or `error_message` does not match `ok`. See [Payments](../payments.md) for the full checkout sequence.

### Example <a href="#example-8" id="example-8"></a>

Set `ARX_BOT_TOKEN`, `QUERY_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const queryId = process.env.QUERY_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !queryId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, QUERY_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.preCheckoutQueriesAnswer(queryId, { key: requestKey });
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
    result = bot.pre_checkout_queries_answer(os.environ["QUERY_ID"], key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let query_id = std::env::var("QUERY_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.pre_checkout_queries_answer(&query_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X POST \
  "https://signal.arx.pro/bot-api/v1/pre_checkout_queries/${QUERY_ID}/answer" \
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

## subscriptions.list <a href="#operation-subscriptions-list" id="operation-subscriptions-list"></a>

`GET /bot-api/v1/subscriptions`

List the subscriptions bought from your bot, one page at a time. Each record has the `subscription_id`, the subscriber, its `plan_id`, a lifecycle `state`, the current period dates, `periods_paid`, `total_paid` by asset, and `has_access`.

`state` is `active`, `past_due`, `canceled`, `expired`, `refunded` or `ended`. Renewals are manual: when a period ends the buyer must approve a new payment, and during the grace period the subscription is `past_due` but keeps access. Use `entitlements.get` for the access decision about one user. Send `next_cursor` back as `cursor` until it is `null`.

### Parameters <a href="#parameters-9" id="parameters-9"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `cursor` | query | string | No | The previous next_cursor; omit or send an empty value for the first page. The value must match `^[A-Za-z0-9_:-]*$`. The maximum length is 128 characters. |
| `limit` | query | integer | No | Maximum subscriptions to return, from 1 to 100. The minimum is 1. The maximum is 100. The default is `20`. |
| `state` | query | `"active"` or `"past_due"` or `"canceled"` or `"expired"` or `"refunded"` or `"ended"` | No | Filter subscriptions by their current state before pagination. |
| `plan_id` | query | string | No | Filter by the exact payment plan ID before pagination. The minimum length is 1 character. The maximum length is 128 characters. |

### Request <a href="#request-9" id="request-9"></a>

Do not send a request body.

### Response <a href="#response-9" id="response-9"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Subscriptions](types/subscriptions.md) in `result` |

### Errors <a href="#errors-9" id="errors-9"></a>

HTTP 400 `invalid_request` means a paging or filter value is malformed. HTTP 403 `capability_not_available` means subscriptions are not available to your bot. HTTP 503 `upstream_unavailable` means the list could not be read; wait and retry. See [Payments](../payments.md).

### Example <a href="#example-9" id="example-9"></a>

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
  const result = await bot.subscriptionsList();
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
    result = bot.subscriptions_list()
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
    let result = bot.subscriptions_list(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X GET \
  "https://signal.arx.pro/bot-api/v1/subscriptions" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "items": [],
    "next_cursor": null
  }
}
```

## subscriptions.get <a href="#operation-subscriptions-get" id="operation-subscriptions-get"></a>

`GET /bot-api/v1/subscriptions/{subscription_id}`

Read one subscription your bot sold. The response has the `subscription_id`, the subscriber, its `plan_id`, the lifecycle `state`, the current paid period and the access deadline.

Use `subscriptions.list` to page through all subscriptions and `entitlements.get` for the access decision about one user.

### Parameters <a href="#parameters-10" id="parameters-10"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `subscription_id` | path | string | Yes | The subscription ID owned by this bot. The minimum length is 1 character. The maximum length is 128 characters. |

### Request <a href="#request-10" id="request-10"></a>

Do not send a request body.

### Response <a href="#response-10" id="response-10"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Subscription](types/subscription.md) in `result` |

### Errors <a href="#errors-10" id="errors-10"></a>

HTTP 403 `capability_not_available` means subscriptions are not available to your bot. HTTP 404 `subscription_not_found` means the subscription does not exist or belongs to another bot. HTTP 503 `upstream_unavailable` means the subscription could not be read; wait and retry. See [Payments](../payments.md).

### Example <a href="#example-10" id="example-10"></a>

Set `ARX_BOT_TOKEN`, `SUBSCRIPTION_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const subscriptionId = process.env.SUBSCRIPTION_ID;
if (!arxBotToken || !subscriptionId) {
  throw new Error("Set ARX_BOT_TOKEN, SUBSCRIPTION_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.subscriptionsGet(subscriptionId);
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
    result = bot.subscriptions_get(os.environ["SUBSCRIPTION_ID"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let subscription_id = std::env::var("SUBSCRIPTION_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.subscriptions_get(&subscription_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X GET \
  "https://signal.arx.pro/bot-api/v1/subscriptions/${SUBSCRIPTION_ID}" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "access_until": "2030-02-01T00:00:00Z",
    "current_period_end_at": "2030-02-01T00:00:00Z",
    "periods_paid": 1,
    "plan_id": "plan-1",
    "started_at": "2030-01-01T00:00:00Z",
    "state": "active",
    "subscription_id": "sub-1",
    "total_paid": [],
    "user": {
      "id": "10003",
      "is_bot": false,
      "name": "Ana López"
    }
  }
}
```

## subscriptions.update <a href="#operation-subscriptions-update" id="operation-subscriptions-update"></a>

`PATCH /bot-api/v1/subscriptions/{subscription_id}`

Cancel the renewal of a subscription your bot sold. Send the subscriber's `user_id` and `is_canceled: true`. The response is the updated subscription.

Cancelling stops future renewals; it is not a refund. Use `charges.refund` to return money for a charge. Cancellation is the only supported change, so `is_canceled: false` or other fields are refused. Send an `Idempotency-Key` header and reuse the same key and body when retrying.

### Parameters <a href="#parameters-11" id="parameters-11"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `subscription_id` | path | string | Yes | The subscription ID owned by this bot. The minimum length is 1 character. The maximum length is 128 characters. |

### Request <a href="#request-11" id="request-11"></a>

Content type: `application/json`. You must include the body.

[UpdateSubscription](types/updatesubscription.md)

Cancel the specified buyer's subscription without refunding confirmed payments. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `is_canceled` | `true` | Yes | Cancel future renewals. Re-enabling or extending access is not supported by this operation. |
| `user_id` | string | Yes | Public ID of this subscription's buyer, available to this bot. The value must match `^[1-9][0-9]*$`. |

### Response <a href="#response-11" id="response-11"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Subscription](types/subscription.md) in `result` |

### Errors <a href="#errors-11" id="errors-11"></a>

HTTP 403 `capability_not_available` means the requested change is not supported; send `is_canceled: true`. HTTP 404 `subscription_not_found` means the subscription is not available to your bot. HTTP 409 `idempotency_key_reused` means the key belongs to another request. See [Payments](../payments.md).

### Example <a href="#example-11" id="example-11"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY`, `SUBSCRIPTION_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
const subscriptionId = process.env.SUBSCRIPTION_ID;
if (!arxBotToken || !requestKey || !subscriptionId) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, SUBSCRIPTION_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.subscriptionsUpdate(subscriptionId, { key: requestKey });
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
    result = bot.subscriptions_update(os.environ["SUBSCRIPTION_ID"], key=os.environ["REQUEST_KEY"])
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
    let subscription_id = std::env::var("SUBSCRIPTION_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.subscriptions_update(&subscription_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X PATCH \
  "https://signal.arx.pro/bot-api/v1/subscriptions/${SUBSCRIPTION_ID}" \
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
    "access_until": "2030-02-01T00:00:00Z",
    "current_period_end_at": "2030-02-01T00:00:00Z",
    "periods_paid": 1,
    "plan_id": "plan-1",
    "started_at": "2030-01-01T00:00:00Z",
    "state": "canceled",
    "subscription_id": "sub-1",
    "total_paid": [],
    "user": {
      "id": "10003",
      "is_bot": false,
      "name": "Ana López"
    }
  }
}
```

## subscriptions.extend <a href="#operation-subscriptions-extend" id="operation-subscriptions-extend"></a>

`POST /bot-api/v1/subscriptions/{subscription_id}/extend`

Reserved for extending a subscription. This operation is not available yet.

Renewals are manual: when a period ends, the buyer approves a new payment from their wallet. See [Subscriptions](../payments/subscriptions.md).

### Parameters <a href="#parameters-12" id="parameters-12"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `subscription_id` | path | string | Yes | The subscription ID owned by this bot. The minimum length is 1 character. The maximum length is 128 characters. |

### Request <a href="#request-12" id="request-12"></a>

Content type: `application/json`. You must include the body.

The value has the type object.

### Response <a href="#response-12" id="response-12"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [PaymentResult](types/paymentresult.md) in `result` |

### Errors <a href="#errors-12" id="errors-12"></a>

HTTP 403 `capability_not_available` is returned for every valid call until the operation is available. A request without a valid `Idempotency-Key` returns HTTP 400 `invalid_request`. See [Payments](../payments.md).

### Example <a href="#example-12" id="example-12"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY`, `SUBSCRIPTION_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
const subscriptionId = process.env.SUBSCRIPTION_ID;
if (!arxBotToken || !requestKey || !subscriptionId) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, SUBSCRIPTION_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.subscriptionsExtend(subscriptionId, { key: requestKey });
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
    result = bot.subscriptions_extend(os.environ["SUBSCRIPTION_ID"], key=os.environ["REQUEST_KEY"])
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
    let subscription_id = std::env::var("SUBSCRIPTION_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.subscriptions_extend(&subscription_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 10 -X POST \
  "https://signal.arx.pro/bot-api/v1/subscriptions/${SUBSCRIPTION_ID}/extend" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {}
}
```

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
