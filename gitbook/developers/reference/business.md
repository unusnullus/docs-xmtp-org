# Business connections

Read a business connection, update the connected account, and exchange encrypted messages.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`business_account.bio.set`](#operation-business-account-bio-set) | `PUT /business/account/bio` |
| [`business_account.username.set`](#operation-business-account-username-set) | `PUT /business/account/username` |
| [`business_connection.endpoint_report.get`](#operation-business-connection-endpoint-report-get) | `GET /business/endpoint-report` |
| [`business_connection.endpoint_report.set`](#operation-business-connection-endpoint-report-set) | `PUT /business/endpoint-report` |
| [`business_connections.get`](#operation-business-connections-get) | `GET /business_connections/{connection_id}` |
| [`business_account.delete_photo`](#operation-business-account-delete-photo) | `DELETE /business_connections/{connection_id}/account/photo` |
| [`business_account.set_photo`](#operation-business-account-set-photo) | `PUT /business_connections/{connection_id}/account/photo` |
| [`business_envelopes.send`](#operation-business-envelopes-send) | `POST /business_connections/{connection_id}/chats/{chat_id}/envelopes` |
| [`business_peer_devices.list`](#operation-business-peer-devices-list) | `GET /business_connections/{connection_id}/chats/{chat_id}/peer_devices` |
| [`business_endpoint.get`](#operation-business-endpoint-get) | `GET /business_connections/{connection_id}/endpoint` |
| [`business_endpoint.register`](#operation-business-endpoint-register) | `POST /business_connections/{connection_id}/endpoint` |
| [`business_endpoint.upload_prekeys`](#operation-business-endpoint-upload-prekeys) | `POST /business_connections/{connection_id}/endpoint/prekeys` |
| [`business_endpoint.reset`](#operation-business-endpoint-reset) | `POST /business_connections/{connection_id}/endpoint/reset` |
| [`business_owner_devices.list`](#operation-business-owner-devices-list) | `GET /business_connections/{connection_id}/owner_devices` |

## business_account.bio.set <a href="#operation-business-account-bio-set" id="operation-business-account-bio-set"></a>

`PUT /bot-api/v1/business/account/bio`

Set the biography of the human account connected to your bot. Send the `business_connection_id` and the exact `bio` text; an empty `bio` clears it.

The connected person must have granted `can_edit_bio`, and the grant is checked again on each request. `bio` accepts zero to 40 UTF-16 code units, so 20 two-unit emoji reach the limit. The text is stored as sent, without trimming. The SDK helper `validate_business_bio` (`validateBusinessBio` in TypeScript) checks the length before you call the API.

An `Idempotency-Key` header is required. After a lost response, retry with the same key and body. The result is `true` once the change is accepted, including when the text was already set.

### Parameters <a href="#parameters" id="parameters"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request" id="request"></a>

Content type: `application/json`. You must include the body.

[SetBusinessAccountBio](types/setbusinessaccountbio.md)

The connected account and its new biography. An empty biography clears the existing text. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `bio` | string | Yes | Zero to forty UTF-16 code units; exact text, empty clears. The owning User validator enforces this unit. |
| `business_connection_id` | string | Yes | The connection identifying the human account whose biography is being changed. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessAccountBioAccepted](types/businessaccountbioaccepted.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

HTTP 403 `not_enough_rights` with reason `can_edit_bio_required` means the person has not granted `can_edit_bio`. HTTP 400 `invalid_request` with field `bio` means the text exceeds 40 UTF-16 code units. HTTP 409 `idempotency_key_reused` means the key was used with a different body. HTTP 409 `version_conflict` means the permissions changed during the request; read the [connection](business.md#operation-business-connections-get) before retrying. HTTP 404 `operation_not_found` with reason `business_connection_unavailable` means the connection does not exist or belongs to another bot. HTTP 403 `capability_not_available` means the connection is not `active` or business mode is off. HTTP 409 `idempotency_in_progress` means the first request with this key is still running; retry later with the same key.

### Example <a href="#example" id="example"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.SetBusinessAccountBio = {
    "bio": "Available for support",
    "business_connection_id": "example-connection"
  };
  const result = await bot.businessAccountBioSet({ body, key: requestKey });
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
    body = models.SetBusinessAccountBio(
        bio='Available for support',
        business_connection_id='example-connection',
    )
    result = bot.business_account_bio_set(body=body, key=os.environ["REQUEST_KEY"])
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
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::SetBusinessAccountBio::new(
        "Available for support".to_owned(),
        "example-connection".to_owned(),
    );
    let query = [];
    let result = bot.business_account_bio_set(&query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/business/account/bio" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"bio": "Available for support", "business_connection_id": "example-connection"}'
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

## business_account.username.set <a href="#operation-business-account-username-set" id="operation-business-account-username-set"></a>

`PUT /bot-api/v1/business/account/username`

Set the username of the human account connected to your bot. Send the `business_connection_id` and the new `username`.

The connected person must have granted `can_edit_username`, and the grant is checked again on each request. `username` must contain 8 to 24 uppercase letters or digits. An empty value does not remove the current username. Usernames share one namespace with other accounts, so a free value can be taken before your request arrives.

An `Idempotency-Key` header is required. After a lost response, retry with the same key and body. The result is `true` once the change is accepted, including when the account already used that username.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-1" id="request-1"></a>

Content type: `application/json`. You must include the body.

[SetBusinessAccountUsername](types/setbusinessaccountusername.md)

The connected account and its required new username. Supply the same body and Idempotency-Key when retrying the original request. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `business_connection_id` | string | Yes | The connection identifying the human account whose username is being changed. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `username` | string | Yes | The required new account username, containing 8 to 24 uppercase letters or digits. An empty value does not remove the username. The value must match `^[A-Z0-9]+$`. The minimum length is 8 characters. The maximum length is 24 characters. |

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessAccountUsernameAccepted](types/businessaccountusernameaccepted.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 403 `not_enough_rights` with reason `can_edit_username_required` means the person has not granted `can_edit_username`. HTTP 409 `handle_unavailable` means another account holds that username; choose another with a new key. HTTP 400 `invalid_request` with field `username` means the value breaks the username rules. HTTP 409 `idempotency_key_reused` means the key was used with a different body. HTTP 404 `operation_not_found` with reason `business_connection_unavailable` means the connection does not exist or belongs to another bot. HTTP 403 `capability_not_available` means the connection is not `active` or business mode is off. HTTP 409 `idempotency_in_progress` means the first request with this key is still running; retry later with the same key. HTTP 409 `version_conflict` means the connection's permissions changed during the request; read the [connection](business.md#operation-business-connections-get) before retrying. See [Errors](../errors.md) for idempotent retries.

### Example <a href="#example-1" id="example-1"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.SetBusinessAccountUsername = {
    "business_connection_id": "example-connection",
    "username": "EXAMPLE8"
  };
  const result = await bot.businessAccountUsernameSet({ body, key: requestKey });
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
    body = models.SetBusinessAccountUsername(
        business_connection_id='example-connection',
        username='EXAMPLE8',
    )
    result = bot.business_account_username_set(body=body, key=os.environ["REQUEST_KEY"])
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
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::SetBusinessAccountUsername::new(
        "example-connection".to_owned(),
        "EXAMPLE8".to_owned(),
    );
    let query = [];
    let result = bot.business_account_username_set(&query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/business/account/username" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"business_connection_id": "example-connection", "username": "EXAMPLE8"}'
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

## business_connection.endpoint_report.get <a href="#operation-business-connection-endpoint-report-get" id="operation-business-connection-endpoint-report-get"></a>

`GET /bot-api/v1/business/endpoint-report`

Reserved for reading the report that the installed Business endpoint runtime records for a connection. This operation is not available yet.

Business chats do not depend on it. Read the connection with [business_connections.get](business.md#operation-business-connections-get) and the endpoint with [business_endpoint.get](business.md#operation-business-endpoint-get).

### Parameters <a href="#parameters-2" id="parameters-2"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `business_connection_id` | query | string | Yes | The existing Business connection whose current report DATA is being read; this query never identifies a PUT receipt. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |

### Request <a href="#request-2" id="request-2"></a>

Do not send a request body.

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessEndpointReportReadback](types/businessendpointreportreadback.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 503 `upstream_unavailable` is returned for every valid call until the operation is available; retrying does not help. HTTP 400 `invalid_request` means the query is invalid or the request carries an `Idempotency-Key`. See [Business endpoint](../managed-bots/business-endpoint.md).

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
  const result = await bot.businessConnectionEndpointReportGet();
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
    result = bot.business_connection_endpoint_report_get()
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
    let result = bot.business_connection_endpoint_report_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/business/endpoint-report" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "app_version": null,
    "capabilities": null,
    "report_revision": null,
    "state": "missing"
  }
}
```

## business_connection.endpoint_report.set <a href="#operation-business-connection-endpoint-report-set" id="operation-business-connection-endpoint-report-set"></a>

`PUT /bot-api/v1/business/endpoint-report`

Reserved for recording a report from the installed Business endpoint runtime for a connection. This operation is not available yet.

Business chats do not depend on it. Read the connection with [business_connections.get](business.md#operation-business-connections-get) and the endpoint with [business_endpoint.get](business.md#operation-business-endpoint-get).

### Parameters <a href="#parameters-3" id="parameters-3"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-3" id="request-3"></a>

Content type: `application/json`. You must include the body.

[SetBusinessEndpointReport](types/setbusinessendpointreport.md)

The endpoint version and capabilities to record for a business connection, conditional on the previous report revision. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `app_version` | string | Yes | The application version reported by the endpoint. The value must match `^[!-~]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `business_connection_id` | string | Yes | The business connection whose endpoint report is being recorded. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `capabilities` | Array of string | Yes | The capability names reported by the endpoint. These names do not grant permissions. The maximum is 128 items. Each item must be unique. |
| `capabilities[]` | string | Per item | The value must match `^[A-Za-z0-9_.-]+$`. The minimum length is 1 character. The maximum length is 64 characters. |
| `expected_report_revision` | null or string | Yes | Required null for the first report, otherwise a canonical uint64 decimal below 18446744073709551615. |

### Response <a href="#response-3" id="response-3"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessEndpointReportRecorded](types/businessendpointreportrecorded.md) in `result` |

### Errors <a href="#errors-3" id="errors-3"></a>

HTTP 503 `upstream_unavailable` is returned for every valid call until the operation is available; retrying does not help. HTTP 400 `invalid_request` means the body or the `Idempotency-Key` is invalid. See [Business endpoint](../managed-bots/business-endpoint.md).

### Example <a href="#example-3" id="example-3"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.SetBusinessEndpointReport = {
    "app_version": "1.2.3",
    "business_connection_id": "example-connection",
    "capabilities": [],
    "expected_report_revision": null
  };
  const result = await bot.businessConnectionEndpointReportSet({ body, key: requestKey });
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
    body = models.SetBusinessEndpointReport(
        app_version='1.2.3',
        business_connection_id='example-connection',
        capabilities=[],
        expected_report_revision=None,
    )
    result = bot.business_connection_endpoint_report_set(body=body, key=os.environ["REQUEST_KEY"])
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
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::SetBusinessEndpointReport::new(
        "1.2.3".to_owned(),
        "example-connection".to_owned(),
        vec![],
        None,
    );
    let query = [];
    let result = bot.business_connection_endpoint_report_set(&query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/business/endpoint-report" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"app_version": "1.2.3", "business_connection_id": "example-connection", "capabilities": [], "expected_report_revision": null}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "report_revision": "1",
    "state": "recorded"
  }
}
```

## business_connections.get <a href="#operation-business-connections-get" id="operation-business-connections-get"></a>

`GET /bot-api/v1/business_connections/{connection_id}`

Read a business connection that belongs to your bot. Use it to check the connected account, the permissions in `rights`, and the connection `status` before you call an account, endpoint or envelope operation.

`is_enabled` is true only when the connection is `active` and its registered endpoint and certificate are current. `status` moves through `pending_endpoint`, `pending_certificate`, `active`, `suspended`, `revoking` and `revoked`; `suspended_reason` explains a suspension when available. `user_chat_id` is the ID reserved for the private chat between your bot and the person. Reading the connection does not open a private chat or grant permission to send: the person must start your bot separately, and each operation still checks the current connection and endpoint.

### Parameters <a href="#parameters-4" id="parameters-4"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `connection_id` | path | string | Yes | Use the opaque Business connection ID returned by the owner connection API. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |

### Request <a href="#request-4" id="request-4"></a>

Do not send a request body.

### Response <a href="#response-4" id="response-4"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessConnection](types/businessconnection.md) in `result` |

### Errors <a href="#errors-4" id="errors-4"></a>

HTTP 404 `business_connection_not_found` means the connection does not exist or belongs to another bot; check the `connection_id`. HTTP 403 `capability_not_available` means business mode is not available to your bot. HTTP 503 `upstream_unavailable` means the connection could not be read now; wait and read it again. HTTP 400 `invalid_request` means `connection_id` is malformed. See the [BusinessConnection](types/businessconnection.md) type for every field.

### Example <a href="#example-4" id="example-4"></a>

Set `ARX_BOT_TOKEN`, `CONNECTION_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const connectionId = process.env.CONNECTION_ID;
if (!arxBotToken || !connectionId) {
  throw new Error("Set ARX_BOT_TOKEN, CONNECTION_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.businessConnectionsGet(connectionId);
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
    result = bot.business_connections_get(os.environ["CONNECTION_ID"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let connection_id = std::env::var("CONNECTION_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.business_connections_get(&connection_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 3 -X GET \
  "https://signal.arx.pro/bot-api/v1/business_connections/${CONNECTION_ID}" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "connected_at": "2026-11-03T00:00:00Z",
    "id": "AQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQE",
    "is_enabled": false,
    "rights": {
      "can_delete_all_messages": false,
      "can_delete_sent_messages": false,
      "can_edit_bio": false,
      "can_edit_profile_photo": false,
      "can_edit_username": false,
      "can_read_messages": false,
      "can_reply": false,
      "can_send_contacts": false
    },
    "status": "pending_endpoint",
    "user": {
      "has_photo": false,
      "id": "700001234",
      "is_bot": false,
      "name": "Ana López",
      "username": "ANALOPEZ"
    },
    "user_chat_id": "4398046511200"
  }
}
```

## business_account.delete_photo <a href="#operation-business-account-delete-photo" id="operation-business-account-delete-photo"></a>

`DELETE /bot-api/v1/business_connections/{connection_id}/account/photo`

Remove the current profile photo of the human account connected to your bot. Send an empty body or `{}`.

The connected person must have granted `can_edit_profile_photo`. Deleting does not bring back an earlier photo; the account is left without a profile photo until a new one is set with [business_account.set_photo](business.md#operation-business-account-set-photo).

An `Idempotency-Key` header is required. A retry with the same key returns the original result and does not delete a photo set after the first request.

### Parameters <a href="#parameters-5" id="parameters-5"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `connection_id` | path | string | Yes | Use the opaque Business connection ID returned by the owner connection API. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |

### Request <a href="#request-5" id="request-5"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-5" id="response-5"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessAccountPhotoAccepted](types/businessaccountphotoaccepted.md) in `result` |

### Errors <a href="#errors-5" id="errors-5"></a>

HTTP 403 `not_enough_rights` with reason `can_edit_profile_photo_required` means the person has not granted `can_edit_profile_photo`; read the [connection](business.md#operation-business-connections-get) to see the current `rights`. HTTP 409 `version_conflict` means the connection's permissions changed during the request; read the connection again before retrying. HTTP 409 `idempotency_key_reused` means the key was used for a different request. HTTP 409 `idempotency_in_progress` means the first request with this key is still running; wait, then retry with the same key. HTTP 404 `operation_not_found` means the connection does not exist or belongs to another bot. HTTP 403 `capability_not_available` means the connection is not `active` or business mode is off. See [Errors](../errors.md) for idempotent retries.

### Example <a href="#example-5" id="example-5"></a>

Set `ARX_BOT_TOKEN`, `CONNECTION_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const connectionId = process.env.CONNECTION_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !connectionId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CONNECTION_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.businessAccountDeletePhoto(connectionId, { key: requestKey });
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
    result = bot.business_account_delete_photo(os.environ["CONNECTION_ID"], key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let connection_id = std::env::var("CONNECTION_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.business_account_delete_photo(&connection_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X DELETE \
  "https://signal.arx.pro/bot-api/v1/business_connections/${CONNECTION_ID}/account/photo" \
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

## business_account.set_photo <a href="#operation-business-account-set-photo" id="operation-business-account-set-photo"></a>

`PUT /bot-api/v1/business_connections/{connection_id}/account/photo`

Set the profile photo of the human account connected to your bot. Upload a static JPEG with [files.upload](files.md#operation-files-upload), then send its `file_id` in `photo.photo.file_id` with `photo.type` set to `static`.

The connected person must have granted `can_edit_profile_photo`. The `file_id` must come from [files.upload](files.md#operation-files-upload) by your bot; IDs from profiles, URLs, message attachments or other bots are rejected. Animated photos and the public fallback photo are unavailable, so `is_public` can only be `false`.

An `Idempotency-Key` header is required. Retry a lost response with the same key and body. The result is `true` only after the account photo has changed; a processed file alone is not enough.

### Parameters <a href="#parameters-6" id="parameters-6"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `connection_id` | path | string | Yes | Use the opaque Business connection ID returned by the owner connection API. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |

### Request <a href="#request-6" id="request-6"></a>

Content type: `application/json`. You must include the body.

[SetBusinessAccountPhoto](types/setbusinessaccountphoto.md)

The new photo for the connected account. Supply the same body and Idempotency-Key when retrying the original request. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `is_public` | `false` | No | Whether to set the public fallback photo. Only false is supported. The default is `false`. |
| `photo` | object | Yes | This contains the photo sizes or photo source for the object. Only the listed fields are allowed. |
| `photo.photo` | object | Yes | This contains the photo sizes or photo source for the object. Only the listed fields are allowed. |
| `photo.photo.file_id` | string | Yes | A new static JPEG uploaded by this same bot through files.upload; copied profile, URL, attachment and foreign-bot references are rejected. The minimum length is 1 character. The maximum length is 8,192 characters. |
| `photo.type` | `"static"` | Yes | The content type is static. |

### Response <a href="#response-6" id="response-6"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessAccountPhotoAccepted](types/businessaccountphotoaccepted.md) in `result` |

### Errors <a href="#errors-6" id="errors-6"></a>

HTTP 403 `not_enough_rights` with reason `can_edit_profile_photo_required` means the person has not granted `can_edit_profile_photo`. HTTP 403 `capability_not_available` means you requested an animated or public photo, the connection is not `active`, or business mode is off. HTTP 404 `file_not_found` means the `file_id` is not a usable upload from your bot; upload the JPEG again. HTTP 409 `idempotency_key_reused` means the key was used with a different body. HTTP 404 `operation_not_found` means the connection does not exist or belongs to another bot. HTTP 400 `invalid_parameter` means the body is malformed. HTTP 409 `version_conflict` means the permissions changed during the request. A `file_id` that is your own upload but not a JPEG within the photo size limit returns HTTP 503 `upstream_unavailable`; upload a valid JPEG instead of retrying. See [Files](../files.md).

### Example <a href="#example-6" id="example-6"></a>

Set `ARX_BOT_TOKEN`, `CONNECTION_ID`, `FILE_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models, FileInput } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const connectionId = process.env.CONNECTION_ID;
const fileId = process.env.FILE_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !connectionId || !fileId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CONNECTION_ID, FILE_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.SetBusinessAccountPhoto = {
    "is_public": false,
    "photo": {
      "photo": FileInput.fromId(fileId),
      "type": "static"
    }
  };
  const result = await bot.businessAccountSetPhoto(connectionId, { body, key: requestKey });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models, FileInput

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.SetBusinessAccountPhoto(
        is_public=False,
        photo=models.SetBusinessAccountPhotoPhoto(
            photo=models.SetBusinessAccountPhotoPhotoPhoto(file_id=os.environ["FILE_ID"]),
            type='static',
        ),
    )
    result = bot.business_account_set_photo(os.environ["CONNECTION_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models, FileInput };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let connection_id = std::env::var("CONNECTION_ID")?;
    let file_id = std::env::var("FILE_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::SetBusinessAccountPhoto {
        is_public: Some(false),
        ..models::SetBusinessAccountPhoto::new(
            models::SetBusinessAccountPhotoPhoto::new(
                models::SetBusinessAccountPhotoPhotoPhoto::new(file_id),
            ),
        )
    };
    let query = [];
    let result = bot.business_account_set_photo(&connection_id, &query, Some(&body), Some(&request_key)).await?;
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
print(json.dumps({'is_public': False, 'photo': {'photo': {'file_id': os.environ['FILE_ID']}, 'type': 'static'}}))
PY
)

curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/business_connections/${CONNECTION_ID}/account/photo" \
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
  "result": true
}
```

## business_envelopes.send <a href="#operation-business-envelopes-send" id="operation-business-envelopes-send"></a>

`POST /bot-api/v1/business_connections/{connection_id}/chats/{chat_id}/envelopes`

Send one encrypted operation in a business chat, such as a message, edit, deletion, pin, read receipt or button answer. The Business runtime builds this request for you, for example through `send_text` (`sendText` in TypeScript).

Each `envelopes` entry is a ciphertext for one device. Include a copy for every peer device and every owner device; owner copies set `conversation_peer`, peer copies omit it. All copies carry the same `content_commitment`, and the set is accepted or rejected as a whole. The person's `rights` are checked for each operation, and the reply window for messages, edits, pins and chat actions.

An `Idempotency-Key` header is required. On retry, keep the key, `message_ref` and body unchanged, and do not encrypt again; the runtime reuses its saved ciphertexts. Success means the operation was recorded for delivery, not that a device received it.

### Parameters <a href="#parameters-7" id="parameters-7"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `connection_id` | path | string | Yes | Use the opaque Business connection ID returned by the owner connection API. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-7" id="request-7"></a>

Content type: `application/json`. You must include the body.

[SendBusinessEnvelopes](types/sendbusinessenvelopes.md)

One encrypted Business operation with its complete set of device copies. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `callback_query_id` | string | No | The received button query ID, required for callback_answer. The maximum length is 256 characters. |
| `content_commitment` | string | Yes | The same 32-byte content commitment, encoded in base64, on every device copy. The minimum length is 44 characters. The maximum length is 44 characters. |
| `dice_emoji` | string | No | Reserved. Business dice is currently unavailable. The maximum length is 16 characters. |
| `envelopes` | Array of object | Yes | The complete set of encrypted device copies for this operation. The minimum is 1 item. The maximum is 150 items. |
| `envelopes[]` | object | Per item | Only the listed fields are allowed. |
| `envelopes[].ciphertext` | string | Yes | The Signal ciphertext addressed to this device, encoded in base64. The minimum length is 1 character. The maximum length is 16,000 characters. |
| `envelopes[].content_commitment` | string | Yes | The same content commitment as the complete operation. The minimum length is 44 characters. The maximum length is 44 characters. |
| `envelopes[].conversation_peer` | string | No | The peer registration ID on an owner-device copy. Omit it on peer-device copies. The maximum length is 256 characters. |
| `envelopes[].to` | string | Yes | The explicit registration ID and installation token of a current peer or owner device. The maximum length is 512 characters. |
| `envelopes[].type` | string | Yes | The encrypted event type allowed for the selected operation. |
| `message_id` | string | No | The original message UUID, required for edit, pin and unpin. The format is `uuid`. |
| `message_ref` | string | Yes | The UUID identifying this operation. Reuse it with the same body and Idempotency-Key. The format is `uuid`. |
| `operation` | `"message"` or `"edit"` or `"delete"` or `"pin"` or `"unpin"` or `"chat_action"` or `"read"` or `"callback_answer"` or `"session_heal"` | Yes | The Business operation represented by every device copy. |
| `target_message_ids` | Array of string | No | The one through 100 message UUIDs in this chat to delete. Required for delete. The maximum is 100 items. Each item must be unique. |
| `target_message_ids[]` | string | Per item | The format is `uuid`. |

### Response <a href="#response-7" id="response-7"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessEnvelopeAcceptance](types/businessenvelopeacceptance.md) in `result` |

### Errors <a href="#errors-7" id="errors-7"></a>

HTTP 400 `business_owner_copies_missing` means an owner device copy is missing. HTTP 400 `business_commitment_mismatch` means the copies differ in commitment. HTTP 403 `business_recipient_not_allowed` means a `to` address is not a current device or a copy's `conversation_peer` is wrong; list the devices again. HTTP 403 `business_right_missing` names the missing right in `required_right`. HTTP 403 `business_reply_window_closed` means the window has closed. HTTP 403 `business_mode_disabled` means business mode is off for your bot. HTTP 403 `business_connection_inactive` and HTTP 409 `business_endpoint_not_registered` mean the connection or endpoint is not active. HTTP 403 `business_chat_not_in_scope` means the chat is outside the connection's scope. HTTP 400 `business_peer_client_outdated` means a `to` device runs an outdated app; list the devices again. HTTP 400 `message_cant_be_deleted` with reason `not_sent_by_bot` means a delete targets a message your bot did not send. HTTP 409 `idempotency_key_reused` means the key belongs to a different body. See [business_peer_devices.list](business.md#operation-business-peer-devices-list).

### Example <a href="#example-7" id="example-7"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `CONNECTION_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const connectionId = process.env.CONNECTION_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !connectionId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, CONNECTION_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.businessEnvelopesSend(connectionId, chatId, { key: requestKey });
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
    result = bot.business_envelopes_send(os.environ["CONNECTION_ID"], os.environ["CHAT_ID"], key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let connection_id = std::env::var("CONNECTION_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.business_envelopes_send(&connection_id, &chat_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/business_connections/${CONNECTION_ID}/chats/${CHAT_ID}/envelopes" \
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
    "message_ref": "6f1c2b9e-4d3a-4c1e-9b7a-2e5f8d0a1c34",
    "accepted": [
      "device-1",
      "device-2"
    ],
    "rejected": []
  }
}
```

## business_peer_devices.list <a href="#operation-business-peer-devices-list" id="operation-business-peer-devices-list"></a>

`GET /bot-api/v1/business_connections/{connection_id}/chats/{chat_id}/peer_devices`

List the public key bundles of the other person's devices in a business chat. Your bot encrypts one copy of each operation per device. The Business runtime calls this before it encrypts a send and when an unknown device writes; call it yourself only if you build envelopes without the runtime.

The chat must be in the connection's current scope. Each bundle has an `identity_key`, a `signed_prekey` and a one-time `prekey`. Each UTC day, one-time prekeys are capped at ten per connection and peer and two thousand per connection; devices on an outdated app are left out of the list; after the cap, `prekey` is `null` and you use the signed prekey alone.

An `Idempotency-Key` header is required. The same request with the same key returns the stored response for up to 24 hours and consumes no further one-time prekeys. Use a new key after the device set or permissions change.

### Parameters <a href="#parameters-8" id="parameters-8"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `connection_id` | path | string | Yes | Use the opaque Business connection ID returned by the owner connection API. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-8" id="request-8"></a>

Do not send a request body.

### Response <a href="#response-8" id="response-8"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessDeviceBundles](types/businessdevicebundles.md) in `result` |

### Errors <a href="#errors-8" id="errors-8"></a>

HTTP 403 `capability_not_available` means business mode is not available or the chat is outside the connection's scope. HTTP 400 `invalid_request` means the `Idempotency-Key` is missing or invalid. HTTP 409 `version_conflict` means the key belongs to a different request. HTTP 503 `upstream_unavailable` means no bundle could be read, including when the connection is not `active` or not available to your bot; read the [endpoint](business.md#operation-business-endpoint-get) and retry later with the same key. See [business_envelopes.send](business.md#operation-business-envelopes-send).

### Example <a href="#example-8" id="example-8"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `CONNECTION_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const connectionId = process.env.CONNECTION_ID;
if (!arxBotToken || !chatId || !connectionId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, CONNECTION_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.businessPeerDevicesList(connectionId, chatId);
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
    result = bot.business_peer_devices_list(os.environ["CONNECTION_ID"], os.environ["CHAT_ID"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let connection_id = std::env::var("CONNECTION_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.business_peer_devices_list(&connection_id, &chat_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 25 -X GET \
  "https://signal.arx.pro/bot-api/v1/business_connections/${CONNECTION_ID}/chats/${CHAT_ID}/peer_devices" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "devices": [
      {
        "endpoint_kind": "human_device",
        "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
        "installation_token": "human-public-example",
        "prekey": null,
        "signed_prekey": {
          "id": 7,
          "key": "BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP",
          "signature": "V7hQmCJed57tA+IXtLLmSsvqjPyc/tSgfsxcq/kKxN/NYOxSVDWExesP727FX6acMoVuVVuJl1qR0uyjnpKABQ=="
        }
      }
    ],
    "native_address": "0x1111111111111111111111111111111111111111",
    "registration_id": "20001"
  }
}
```

## business_endpoint.get <a href="#operation-business-endpoint-get" id="operation-business-endpoint-get"></a>

`GET /bot-api/v1/business_connections/{connection_id}/endpoint`

Read your bot's endpoint for a business connection. Business chats are end-to-end encrypted, and the endpoint is the encryption identity that the SDK's Business runtime keeps in your process. Check `status`, `prekeys_remaining`, `fingerprint` and the human-signed `certificate`.

`status` is `pending_certificate` until the connected person approves the endpoint, then `active` or `suspended`. Your bot can act in business chats only while it is `active` with a current `certificate`; this response is not a permission on its own. `certificate` is `null` before approval. The read consumes no one-time prekeys. When `prekeys_remaining` runs low, call [business_endpoint.upload_prekeys](business.md#operation-business-endpoint-upload-prekeys).

### Parameters <a href="#parameters-9" id="parameters-9"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `connection_id` | path | string | Yes | Use the opaque Business connection ID returned by the owner connection API. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |

### Request <a href="#request-9" id="request-9"></a>

Do not send a request body.

### Response <a href="#response-9" id="response-9"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessEndpoint](types/businessendpoint.md) in `result` |

### Errors <a href="#errors-9" id="errors-9"></a>

HTTP 401 `unauthorized` is also returned when the connection does not exist, belongs to another bot, or has no registered endpoint yet. HTTP 403 `capability_not_available` means business mode is not available to your bot or the connection is unavailable. HTTP 503 `upstream_unavailable` means the endpoint could not be read now; wait and retry. See [Security](../security.md).

### Example <a href="#example-9" id="example-9"></a>

Set `ARX_BOT_TOKEN`, `CONNECTION_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const connectionId = process.env.CONNECTION_ID;
if (!arxBotToken || !connectionId) {
  throw new Error("Set ARX_BOT_TOKEN, CONNECTION_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.businessEndpointGet(connectionId);
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
    result = bot.business_endpoint_get(os.environ["CONNECTION_ID"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let connection_id = std::env::var("CONNECTION_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.business_endpoint_get(&connection_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/business_connections/${CONNECTION_ID}/endpoint" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "cert_seq": "0",
    "certificate": null,
    "endpoint_kind": "bot_connection",
    "endpoint_pop": {
      "payload": {
        "bot_id": "10001",
        "connection_id": "AAECAwQFBgcICQoLDA0ODw",
        "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
        "owner_registration_id": "20001",
        "signed_prekey_id": 7
      },
      "signature": "Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA=="
    },
    "fingerprint": null,
    "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
    "installation_token": "botc_00000000000000000000000000000000",
    "prekeys_remaining": 0,
    "status": "pending_certificate"
  }
}
```

## business_endpoint.register <a href="#operation-business-endpoint-register" id="operation-business-endpoint-register"></a>

`POST /bot-api/v1/business_connections/{connection_id}/endpoint`

Register your bot's encryption endpoint for a business connection. The SDK's Business runtime generates the keys, stores them in its encrypted local state, and calls this operation through `register`. Only public material is uploaded: `identity_key`, `signed_prekey`, up to 100 one-time `prekeys`, and `endpoint_pop`, which proves your process holds the private identity key. Private keys never leave your process.

Registration moves the connection to `pending_certificate`. The connected person must approve the endpoint before your bot can act in business chats; read [business_endpoint.get](business.md#operation-business-endpoint-get) until `status` is `active`.

Keep the body unchanged on every retry. The runtime saves it before the first request and resends it after a timeout or restart; it never generates new keys on its own. To replace the identity, use [business_endpoint.reset](business.md#operation-business-endpoint-reset).

### Parameters <a href="#parameters-10" id="parameters-10"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `connection_id` | path | string | Yes | Use the opaque Business connection ID returned by the owner connection API. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |

### Request <a href="#request-10" id="request-10"></a>

Content type: `application/json`. You must include the body.

[RegisterBusinessEndpoint](types/registerbusinessendpoint.md)

Public Business endpoint data for RegisterBusinessEndpoint. No runtime private key is accepted or returned. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `endpoint_pop` | object | Yes | The typed runtime proof of possession, with its bound payload and XEdDSA signature. Only the listed fields are allowed. |
| `endpoint_pop.payload` | object | Yes | The exact typed fields signed by this proof or human certificate. Only the listed fields are allowed. |
| `endpoint_pop.payload.bot_id` | string | Yes | The bound account identifier as a canonical decimal string. The value must match `^[1-9][0-9]{0,19}$`. |
| `endpoint_pop.payload.connection_id` | string | Yes | The opaque canonical Base64url Business connection identifier. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |
| `endpoint_pop.payload.identity_key` | string | Yes | The canonical padded Base64 serialized Signal public key, including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `endpoint_pop.payload.owner_registration_id` | string | Yes | The bound account identifier as a canonical decimal string. The value must match `^[1-9][0-9]{0,19}$`. |
| `endpoint_pop.payload.signed_prekey_id` | integer | Yes | The nonnegative 32-bit Signal prekey identifier. The minimum is 0. The maximum is 2,147,483,647. |
| `endpoint_pop.signature` | string | Yes | The canonical padded Base64 XEdDSA signature. The minimum length is 88 characters. The maximum length is 88 characters. |
| `identity_key` | string | Yes | The canonical padded Base64 serialized Signal public key, including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `prekeys` | Array of [BusinessEndpointPrekey](types/businessendpointprekey.md) | Yes | Public one-time prekeys to add. Consumed IDs cannot be reused for this endpoint identity. The maximum is 100 items. |
| `signed_prekey` | [BusinessEndpointSignedPrekey](types/businessendpointsignedprekey.md) | Yes | The public signed prekey and its identity-key signature. |

### Response <a href="#response-10" id="response-10"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessEndpoint](types/businessendpoint.md) in `result` |

### Errors <a href="#errors-10" id="errors-10"></a>

HTTP 400 `invalid_request` means a key, signature or `endpoint_pop` payload does not match this connection and bot. HTTP 409 `version_conflict` means another identity is registered, the 10-minute registration window after the connection was created has expired, or the connection changed; read the endpoint before acting. HTTP 403 `capability_not_available` means business mode is not available to your bot or the connection is no longer usable. HTTP 401 `unauthorized` is also returned when the connection does not exist or belongs to another bot. See [Security](../security.md).

### Example <a href="#example-10" id="example-10"></a>

Set `ARX_BOT_TOKEN`, `CONNECTION_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const connectionId = process.env.CONNECTION_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !connectionId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CONNECTION_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.RegisterBusinessEndpoint = {
    "endpoint_pop": {
      "payload": {
        "bot_id": "10001",
        "connection_id": "AAECAwQFBgcICQoLDA0ODw",
        "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
        "owner_registration_id": "20001",
        "signed_prekey_id": 7
      },
      "signature": "Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA=="
    },
    "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
    "prekeys": [],
    "signed_prekey": {
      "id": 7,
      "key": "BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP",
      "signature": "V7hQmCJed57tA+IXtLLmSsvqjPyc/tSgfsxcq/kKxN/NYOxSVDWExesP727FX6acMoVuVVuJl1qR0uyjnpKABQ=="
    }
  };
  const result = await bot.businessEndpointRegister(connectionId, { body, key: requestKey });
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
    body = models.RegisterBusinessEndpoint(
        endpoint_pop=models.RegisterBusinessEndpointEndpointPop(
            payload=models.RegisterBusinessEndpointEndpointPopPayload(
                bot_id='10001',
                connection_id='AAECAwQFBgcICQoLDA0ODw',
                identity_key='BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx',
                owner_registration_id='20001',
                signed_prekey_id=7,
            ),
            signature='Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA==',
        ),
        identity_key='BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx',
        prekeys=[],
        signed_prekey=models.BusinessEndpointSignedPrekey(
            id=7,
            key='BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP',
            signature='V7hQmCJed57tA+IXtLLmSsvqjPyc/tSgfsxcq/kKxN/NYOxSVDWExesP727FX6acMoVuVVuJl1qR0uyjnpKABQ==',
        ),
    )
    result = bot.business_endpoint_register(os.environ["CONNECTION_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let connection_id = std::env::var("CONNECTION_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::RegisterBusinessEndpoint::new(
        models::RegisterBusinessEndpointEndpointPop::new(
            models::RegisterBusinessEndpointEndpointPopPayload::new(
                "10001".to_owned(),
                "AAECAwQFBgcICQoLDA0ODw".to_owned(),
                "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx".to_owned(),
                "20001".to_owned(),
                7,
            ),
            "Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA==".to_owned(),
        ),
        "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx".to_owned(),
        vec![],
        models::BusinessEndpointSignedPrekey::new(
            7,
            "BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP".to_owned(),
            "V7hQmCJed57tA+IXtLLmSsvqjPyc/tSgfsxcq/kKxN/NYOxSVDWExesP727FX6acMoVuVVuJl1qR0uyjnpKABQ==".to_owned(),
        ),
    );
    let query = [];
    let result = bot.business_endpoint_register(&connection_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/business_connections/${CONNECTION_ID}/endpoint" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"endpoint_pop": {"payload": {"bot_id": "10001", "connection_id": "AAECAwQFBgcICQoLDA0ODw", "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx", "owner_registration_id": "20001", "signed_prekey_id": 7}, "signature": "Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA=="}, "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx", "prekeys": [], "signed_prekey": {"id": 7, "key": "BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP", "signature": "V7hQmCJed57tA+IXtLLmSsvqjPyc/tSgfsxcq/kKxN/NYOxSVDWExesP727FX6acMoVuVVuJl1qR0uyjnpKABQ=="}}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "cert_seq": "0",
    "certificate": null,
    "endpoint_kind": "bot_connection",
    "endpoint_pop": {
      "payload": {
        "bot_id": "10001",
        "connection_id": "AAECAwQFBgcICQoLDA0ODw",
        "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
        "owner_registration_id": "20001",
        "signed_prekey_id": 7
      },
      "signature": "Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA=="
    },
    "fingerprint": null,
    "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
    "installation_token": "botc_00000000000000000000000000000000",
    "prekeys_remaining": 0,
    "status": "pending_certificate"
  }
}
```

## business_endpoint.upload_prekeys <a href="#operation-business-endpoint-upload-prekeys" id="operation-business-endpoint-upload-prekeys"></a>

`POST /bot-api/v1/business_connections/{connection_id}/endpoint/prekeys`

Add public one-time prekeys to your bot's business endpoint. A person's device can use one when it opens a new encrypted session with your bot. Send 1 to 100 `prekeys`; they are added to the existing inventory.

Call this when [business_endpoint.get](business.md#operation-business-endpoint-get) shows a low `prekeys_remaining`. With the Business runtime, call `upload_prekeys` (`uploadPrekeys` in TypeScript) with a stable key and a count. The runtime stores the private keys and the exact upload first, so an interrupted refill resumes with the same key and count; a different count under that key conflicts. Only public keys are uploaded.

Each `id` must be unique and must not repeat an ID already consumed by this endpoint identity. A refill keeps the identity, the signed prekey and the connection status; it does not resume a suspended endpoint.

### Parameters <a href="#parameters-11" id="parameters-11"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `connection_id` | path | string | Yes | Use the opaque Business connection ID returned by the owner connection API. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |

### Request <a href="#request-11" id="request-11"></a>

Content type: `application/json`. You must include the body.

[UploadBusinessEndpointPrekeys](types/uploadbusinessendpointprekeys.md)

Public Business endpoint data for UploadBusinessEndpointPrekeys. No runtime private key is accepted or returned. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `prekeys` | Array of [BusinessEndpointPrekey](types/businessendpointprekey.md) | Yes | Public one-time prekeys to add. Consumed IDs cannot be reused for this endpoint identity. The minimum is 1 item. The maximum is 100 items. |

### Response <a href="#response-11" id="response-11"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessEndpoint](types/businessendpoint.md) in `result` |

### Errors <a href="#errors-11" id="errors-11"></a>

HTTP 400 `invalid_request` means the list is empty, exceeds 100 entries, repeats an `id`, or has an invalid key. HTTP 403 `capability_not_available` means business mode is not available, no endpoint is registered yet, or the connection is unavailable. HTTP 409 `version_conflict` means an `id` was already used by this endpoint identity with another key or was already consumed; choose new IDs. HTTP 401 `unauthorized` is also returned when the connection does not exist or belongs to another bot. See [Limits](../limits.md).

### Example <a href="#example-11" id="example-11"></a>

Set `ARX_BOT_TOKEN`, `CONNECTION_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const connectionId = process.env.CONNECTION_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !connectionId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CONNECTION_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.UploadBusinessEndpointPrekeys = {
    "prekeys": [{
      "id": 1,
      "key": "BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP"
    }]
  };
  const result = await bot.businessEndpointUploadPrekeys(connectionId, { body, key: requestKey });
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
    body = models.UploadBusinessEndpointPrekeys(
        prekeys=[models.BusinessEndpointPrekey(id=1, key='BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP')],
    )
    result = bot.business_endpoint_upload_prekeys(os.environ["CONNECTION_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let connection_id = std::env::var("CONNECTION_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::UploadBusinessEndpointPrekeys::new(
        vec![
            models::BusinessEndpointPrekey::new(
                1,
                "BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP".to_owned(),
            ),
        ],
    );
    let query = [];
    let result = bot.business_endpoint_upload_prekeys(&connection_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/business_connections/${CONNECTION_ID}/endpoint/prekeys" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"prekeys": [{"id": 1, "key": "BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP"}]}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "cert_seq": "0",
    "certificate": null,
    "endpoint_kind": "bot_connection",
    "endpoint_pop": {
      "payload": {
        "bot_id": "10001",
        "connection_id": "AAECAwQFBgcICQoLDA0ODw",
        "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
        "owner_registration_id": "20001",
        "signed_prekey_id": 7
      },
      "signature": "Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA=="
    },
    "fingerprint": null,
    "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
    "installation_token": "botc_00000000000000000000000000000000",
    "prekeys_remaining": 0,
    "status": "pending_certificate"
  }
}
```

## business_endpoint.reset <a href="#operation-business-endpoint-reset" id="operation-business-endpoint-reset"></a>

`POST /bot-api/v1/business_connections/{connection_id}/endpoint/reset`

Replace your bot's endpoint identity for a business connection when its private keys are lost or must change. The body has the shape of [business_endpoint.register](business.md#operation-business-endpoint-register) and carries only public material for the new identity.

A reset suspends the connection and returns `pending_certificate`. Your bot cannot act in business chats until the connected person approves the new identity and a new certificate is issued. The connection must be `active` or `suspended` with an approved endpoint. The Business runtime never starts a reset on its own, even after a timeout; your code decides.

Retry a lost response with the same body. A retry never activates the new identity or extends the time the person has to approve it.

### Parameters <a href="#parameters-12" id="parameters-12"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `connection_id` | path | string | Yes | Use the opaque Business connection ID returned by the owner connection API. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |

### Request <a href="#request-12" id="request-12"></a>

Content type: `application/json`. You must include the body.

[RegisterBusinessEndpoint](types/registerbusinessendpoint.md)

Public Business endpoint data for RegisterBusinessEndpoint. No runtime private key is accepted or returned. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `endpoint_pop` | object | Yes | The typed runtime proof of possession, with its bound payload and XEdDSA signature. Only the listed fields are allowed. |
| `endpoint_pop.payload` | object | Yes | The exact typed fields signed by this proof or human certificate. Only the listed fields are allowed. |
| `endpoint_pop.payload.bot_id` | string | Yes | The bound account identifier as a canonical decimal string. The value must match `^[1-9][0-9]{0,19}$`. |
| `endpoint_pop.payload.connection_id` | string | Yes | The opaque canonical Base64url Business connection identifier. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |
| `endpoint_pop.payload.identity_key` | string | Yes | The canonical padded Base64 serialized Signal public key, including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `endpoint_pop.payload.owner_registration_id` | string | Yes | The bound account identifier as a canonical decimal string. The value must match `^[1-9][0-9]{0,19}$`. |
| `endpoint_pop.payload.signed_prekey_id` | integer | Yes | The nonnegative 32-bit Signal prekey identifier. The minimum is 0. The maximum is 2,147,483,647. |
| `endpoint_pop.signature` | string | Yes | The canonical padded Base64 XEdDSA signature. The minimum length is 88 characters. The maximum length is 88 characters. |
| `identity_key` | string | Yes | The canonical padded Base64 serialized Signal public key, including its type prefix. The minimum length is 44 characters. The maximum length is 44 characters. |
| `prekeys` | Array of [BusinessEndpointPrekey](types/businessendpointprekey.md) | Yes | Public one-time prekeys to add. Consumed IDs cannot be reused for this endpoint identity. The maximum is 100 items. |
| `signed_prekey` | [BusinessEndpointSignedPrekey](types/businessendpointsignedprekey.md) | Yes | The public signed prekey and its identity-key signature. |

### Response <a href="#response-12" id="response-12"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessEndpoint](types/businessendpoint.md) in `result` |

### Errors <a href="#errors-12" id="errors-12"></a>

HTTP 403 `capability_not_available` means there is no approved endpoint to replace or business mode is not available to your bot. HTTP 409 `version_conflict` means a reset is already waiting for approval or the connection changed; read [business_endpoint.get](business.md#operation-business-endpoint-get) first. HTTP 400 `invalid_request` means a key or the `endpoint_pop` payload is invalid. See [Security](../security.md).

### Example <a href="#example-12" id="example-12"></a>

Set `ARX_BOT_TOKEN`, `CONNECTION_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const connectionId = process.env.CONNECTION_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !connectionId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CONNECTION_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.RegisterBusinessEndpoint = {
    "endpoint_pop": {
      "payload": {
        "bot_id": "10001",
        "connection_id": "AAECAwQFBgcICQoLDA0ODw",
        "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
        "owner_registration_id": "20001",
        "signed_prekey_id": 7
      },
      "signature": "Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA=="
    },
    "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
    "prekeys": [],
    "signed_prekey": {
      "id": 7,
      "key": "BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP",
      "signature": "V7hQmCJed57tA+IXtLLmSsvqjPyc/tSgfsxcq/kKxN/NYOxSVDWExesP727FX6acMoVuVVuJl1qR0uyjnpKABQ=="
    }
  };
  const result = await bot.businessEndpointReset(connectionId, { body, key: requestKey });
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
    body = models.RegisterBusinessEndpoint(
        endpoint_pop=models.RegisterBusinessEndpointEndpointPop(
            payload=models.RegisterBusinessEndpointEndpointPopPayload(
                bot_id='10001',
                connection_id='AAECAwQFBgcICQoLDA0ODw',
                identity_key='BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx',
                owner_registration_id='20001',
                signed_prekey_id=7,
            ),
            signature='Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA==',
        ),
        identity_key='BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx',
        prekeys=[],
        signed_prekey=models.BusinessEndpointSignedPrekey(
            id=7,
            key='BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP',
            signature='V7hQmCJed57tA+IXtLLmSsvqjPyc/tSgfsxcq/kKxN/NYOxSVDWExesP727FX6acMoVuVVuJl1qR0uyjnpKABQ==',
        ),
    )
    result = bot.business_endpoint_reset(os.environ["CONNECTION_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let connection_id = std::env::var("CONNECTION_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::RegisterBusinessEndpoint::new(
        models::RegisterBusinessEndpointEndpointPop::new(
            models::RegisterBusinessEndpointEndpointPopPayload::new(
                "10001".to_owned(),
                "AAECAwQFBgcICQoLDA0ODw".to_owned(),
                "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx".to_owned(),
                "20001".to_owned(),
                7,
            ),
            "Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA==".to_owned(),
        ),
        "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx".to_owned(),
        vec![],
        models::BusinessEndpointSignedPrekey::new(
            7,
            "BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP".to_owned(),
            "V7hQmCJed57tA+IXtLLmSsvqjPyc/tSgfsxcq/kKxN/NYOxSVDWExesP727FX6acMoVuVVuJl1qR0uyjnpKABQ==".to_owned(),
        ),
    );
    let query = [];
    let result = bot.business_endpoint_reset(&connection_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/business_connections/${CONNECTION_ID}/endpoint/reset" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"endpoint_pop": {"payload": {"bot_id": "10001", "connection_id": "AAECAwQFBgcICQoLDA0ODw", "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx", "owner_registration_id": "20001", "signed_prekey_id": 7}, "signature": "Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA=="}, "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx", "prekeys": [], "signed_prekey": {"id": 7, "key": "BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP", "signature": "V7hQmCJed57tA+IXtLLmSsvqjPyc/tSgfsxcq/kKxN/NYOxSVDWExesP727FX6acMoVuVVuJl1qR0uyjnpKABQ=="}}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "cert_seq": "0",
    "certificate": null,
    "endpoint_kind": "bot_connection",
    "endpoint_pop": {
      "payload": {
        "bot_id": "10001",
        "connection_id": "AAECAwQFBgcICQoLDA0ODw",
        "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
        "owner_registration_id": "20001",
        "signed_prekey_id": 7
      },
      "signature": "Fe/bq9zxzO8zOHcrGxfkCMhfg6QLhfxbV2b+1OaIUxCqrFoWl3EsGfVbylJS22VPKJ2Re9YsPJFiJy3vFw46BA=="
    },
    "fingerprint": null,
    "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
    "installation_token": "botc_00000000000000000000000000000000",
    "prekeys_remaining": 0,
    "status": "pending_certificate"
  }
}
```

## business_owner_devices.list <a href="#operation-business-owner-devices-list" id="operation-business-owner-devices-list"></a>

`GET /bot-api/v1/business_connections/{connection_id}/owner_devices`

List the public key bundles of the connected person's own devices. Every business operation your bot sends needs an encrypted copy for each of them. The Business runtime calls this before it encrypts a send; call it yourself only if you build envelopes without the runtime.

The endpoint must be `active`. Each bundle has an `identity_key`, a `signed_prekey` and a one-time `prekey`. Each device gives out at most three one-time prekeys per connection per UTC day; after that, or when its inventory is empty, `prekey` is `null` and you use the signed prekey alone.

An `Idempotency-Key` header is required. The same request with the same key returns the stored response for up to 24 hours and consumes no further one-time prekeys. Use a new key after the device set or permissions change.

### Parameters <a href="#parameters-13" id="parameters-13"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `connection_id` | path | string | Yes | Use the opaque Business connection ID returned by the owner connection API. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 22 characters. The maximum length is 128 characters. |

### Request <a href="#request-13" id="request-13"></a>

Do not send a request body.

### Response <a href="#response-13" id="response-13"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [BusinessDeviceBundles](types/businessdevicebundles.md) in `result` |

### Errors <a href="#errors-13" id="errors-13"></a>

HTTP 403 `capability_not_available` means business mode is not available or an owner device runs an outdated app version, which suspends the connection. HTTP 400 `invalid_request` means the `Idempotency-Key` is missing or invalid. HTTP 409 `version_conflict` means the key belongs to a different request. HTTP 503 `upstream_unavailable` means no bundle could be read, including when the connection is not `active` or not available to your bot; read the [endpoint](business.md#operation-business-endpoint-get) and retry later with the same key. See [business_envelopes.send](business.md#operation-business-envelopes-send).

### Example <a href="#example-13" id="example-13"></a>

Set `ARX_BOT_TOKEN`, `CONNECTION_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const connectionId = process.env.CONNECTION_ID;
if (!arxBotToken || !connectionId) {
  throw new Error("Set ARX_BOT_TOKEN, CONNECTION_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.businessOwnerDevicesList(connectionId);
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
    result = bot.business_owner_devices_list(os.environ["CONNECTION_ID"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let connection_id = std::env::var("CONNECTION_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.business_owner_devices_list(&connection_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 25 -X GET \
  "https://signal.arx.pro/bot-api/v1/business_connections/${CONNECTION_ID}/owner_devices" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "devices": [
      {
        "endpoint_kind": "human_device",
        "identity_key": "BQwgdnVBv6DhNGO7gC5zQtaGe8uiAxTdk/OlgIFMNBFx",
        "installation_token": "human-public-example",
        "prekey": null,
        "signed_prekey": {
          "id": 7,
          "key": "BbTNMn1F/XhTUtl8pjMFiVxa8CawLeadCaXlsd3WE/QP",
          "signature": "V7hQmCJed57tA+IXtLLmSsvqjPyc/tSgfsxcq/kKxN/NYOxSVDWExesP727FX6acMoVuVVuJl1qR0uyjnpKABQ=="
        }
      }
    ],
    "native_address": "0x1111111111111111111111111111111111111111",
    "registration_id": "20001"
  }
}
```

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
