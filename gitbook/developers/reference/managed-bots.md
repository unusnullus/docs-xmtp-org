# Managed bots

Read and rotate the tokens of bots you manage and set who can use them.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`managed_bots.get_access`](#operation-managed-bots-get-access) | `GET /managed_bots/{bot_id}/access` |
| [`managed_bots.set_access`](#operation-managed-bots-set-access) | `PUT /managed_bots/{bot_id}/access` |
| [`managed_bots.get_token`](#operation-managed-bots-get-token) | `GET /managed_bots/{bot_id}/token` |
| [`managed_bots.rotate_token`](#operation-managed-bots-rotate-token) | `POST /managed_bots/{bot_id}/token/rotate` |

## managed_bots.get_access <a href="#operation-managed-bots-get-access" id="operation-managed-bots-get-access"></a>

`GET /bot-api/v1/managed_bots/{bot_id}/access`

Read who may use a bot that your bot manages. `is_access_restricted` tells you whether only the owner and listed people can use it, and `added_users` lists those people.

The managed bot's owner always has access and does not appear in `added_users`. The list holds at most ten people. Use `managed_bots.set_access` to change it.

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `bot_id` | path | string | Yes | Use the canonical decimal ID of the bot you currently manage. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request" id="request"></a>

Do not send a request body.

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BotAccessSettings](types/botaccesssettings.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

HTTP 403 `managed_bot_detached` means your bot does not manage this bot now. HTTP 403 `managed_mode_disabled` means managed bots are not available to your bot. HTTP 400 `invalid_request` means `bot_id` is not a canonical decimal ID or the request carried a query string. See [Security](../security.md).

### Example <a href="#example" id="example"></a>

Set `ARX_BOT_TOKEN`, `BOT_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const botId = process.env.BOT_ID;
if (!arxBotToken || !botId) {
  throw new Error("Set ARX_BOT_TOKEN, BOT_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.managedBotsGetAccess(botId);
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
    result = bot.managed_bots_get_access(os.environ["BOT_ID"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let bot_id = std::env::var("BOT_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.managed_bots_get_access(&bot_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/managed_bots/${BOT_ID}/access" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "added_users": [],
    "is_access_restricted": false
  }
}
```

## managed_bots.set_access <a href="#operation-managed-bots-set-access" id="operation-managed-bots-set-access"></a>

`PUT /bot-api/v1/managed_bots/{bot_id}/access`

Set who may use a bot that your bot manages. Send `is_access_restricted: true` with up to ten user IDs in `added_user_ids` to limit the bot to its owner and those people. Send `is_access_restricted: false` to open it to everyone. The response is `true`.

This call replaces the whole list. The owner always has access and does not need to be listed. IDs must be distinct decimal IDs of active people; bot accounts are refused. When access is unrestricted, the IDs you send are not checked or saved (the list must still have at most ten entries), and the saved list is kept. When you restrict access, omitting `added_user_ids` sets an empty list.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `bot_id` | path | string | Yes | Use the canonical decimal ID of the bot you currently manage. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-1" id="request-1"></a>

Content type: `application/json`. You must include the body.

[SetManagedBotAccessSettings](types/setmanagedbotaccesssettings.md)

The desired access settings. The owner always has access and does not need to be listed. Only the listed fields are allowed. If `is_access_restricted` is omitted or is `true`, Each item must be unique.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `added_user_ids` | Array of string | No | Public decimal human user IDs. Omission means an empty list when restricted. Values are ignored when unrestricted. The maximum is 10 items. The default is `[]`. |
| `is_access_restricted` | boolean | Yes | Whether to restrict the bot to the owner and listed people. |

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 400 `invalid_request` means the list has more than ten entries, repeats an ID, or contains an ID that is not a canonical decimal ID. An ID that does not belong to an active person currently returns HTTP 503 `upstream_unavailable`; check the IDs before you retry. HTTP 403 `managed_bot_detached` means your bot does not manage this bot now. HTTP 403 `managed_mode_disabled` means managed bots are not available to your bot. See [Security](../security.md).

### Example <a href="#example-1" id="example-1"></a>

Set `ARX_BOT_TOKEN`, `BOT_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const botId = process.env.BOT_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !botId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, BOT_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.SetManagedBotAccessSettings = {
    "added_user_ids": ["700001234"],
    "is_access_restricted": true
  };
  const result = await bot.managedBotsSetAccess(botId, { body, key: requestKey });
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
    body = models.SetManagedBotAccessSettings(
        added_user_ids=['700001234'],
        is_access_restricted=True,
    )
    result = bot.managed_bots_set_access(os.environ["BOT_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let bot_id = std::env::var("BOT_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::SetManagedBotAccessSettings {
        added_user_ids: Some(vec!["700001234".to_owned()]),
        ..models::SetManagedBotAccessSettings::new(true)
    };
    let query = [];
    let result = bot.managed_bots_set_access(&bot_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/managed_bots/${BOT_ID}/access" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"added_user_ids": ["700001234"], "is_access_restricted": true}'
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

## managed_bots.get_token <a href="#operation-managed-bots-get-token" id="operation-managed-bots-get-token"></a>

`GET /bot-api/v1/managed_bots/{bot_id}/token`

Read the current token of a bot that your bot manages. Use it when your service needs to act as that bot, for example after a restart. Call it with your manager bot's own token and the managed bot's decimal `bot_id`.

The response holds the secret `token`. Treat it like your own bot token: keep it in a secrets store, never log it, never cache the response, and never send it to a client or Mini App. Each read is recorded. If you suspect the token was exposed, replace it with `managed_bots.rotate_token`.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `bot_id` | path | string | Yes | Use the canonical decimal ID of the bot you currently manage. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-2" id="request-2"></a>

Do not send a request body.

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [ManagedBotToken](types/managedbottoken.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 403 `managed_bot_detached` means your bot does not manage this bot now; stop using any copy of its token. HTTP 409 `token_not_issued` means the managed bot has no current token yet. HTTP 403 `managed_mode_disabled` means managed bots are not available to your bot. HTTP 400 `invalid_request` means `bot_id` is not a canonical decimal ID. See [Security](../security.md) for token handling.

### Example <a href="#example-2" id="example-2"></a>

Set `ARX_BOT_TOKEN`, `BOT_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const botId = process.env.BOT_ID;
if (!arxBotToken || !botId) {
  throw new Error("Set ARX_BOT_TOKEN, BOT_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.managedBotsGetToken(botId);
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
    result = bot.managed_bots_get_token(os.environ["BOT_ID"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let bot_id = std::env::var("BOT_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.managed_bots_get_token(&bot_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/managed_bots/${BOT_ID}/token" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "token": "example-current-token"
  }
}
```

## managed_bots.rotate_token <a href="#operation-managed-bots-rotate-token" id="operation-managed-bots-rotate-token"></a>

`POST /bot-api/v1/managed_bots/{bot_id}/token/rotate`

Replace the token of a bot that your bot manages. The previous token stops working at once, so store the new `token` from the response before you switch the managed bot over. Your bot receives a `managed_bot` update, and the managed bot's human owner is notified.

Send an `Idempotency-Key` header and an empty body or `{}`. If the result is uncertain, retry with the same key: you get the token that rotation produced rather than a second rotation. Keep the new token in a secrets store and never log it.

### Parameters <a href="#parameters-3" id="parameters-3"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `bot_id` | path | string | Yes | Use the canonical decimal ID of the bot you currently manage. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-3" id="request-3"></a>

Content type: `application/json`. You must include the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-3" id="response-3"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [ManagedBotToken](types/managedbottoken.md) in `result` |

### Errors <a href="#errors-3" id="errors-3"></a>

HTTP 409 `idempotency_key_reused` means the token changed again after the rotation this key belongs to, or the key is more than 24 hours old; read the current token with `managed_bots.get_token` instead of retrying. HTTP 403 `managed_bot_detached` means your bot no longer manages this bot. HTTP 403 `managed_mode_disabled` means managed bots are not available to your bot. HTTP 400 `invalid_request` means the key is missing or malformed, or the body is not empty. HTTP 503 `upstream_unavailable` means you should retry with the same key after `retry_after`. See [Security](../security.md).

### Example <a href="#example-3" id="example-3"></a>

Set `ARX_BOT_TOKEN`, `BOT_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const botId = process.env.BOT_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !botId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, BOT_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.Empty = {};
  const result = await bot.managedBotsRotateToken(botId, { body, key: requestKey });
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
    body = models.Empty(root={})
    result = bot.managed_bots_rotate_token(os.environ["BOT_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let bot_id = std::env::var("BOT_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = std::collections::BTreeMap::new();
    let query = [];
    let result = bot.managed_bots_rotate_token(&bot_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/managed_bots/${BOT_ID}/token/rotate" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "token": "example-current-token"
  }
}
```

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
