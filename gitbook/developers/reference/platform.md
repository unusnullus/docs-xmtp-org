# Identity and platform

Check your bot's identity, read its effective limits, and manage its token.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`chats.get`](#operation-chats-get) | `GET /chats/{chat_id}` |
| [`me.get`](#operation-me-get) | `GET /me` |
| [`openapi.get`](#operation-openapi-get) | `GET /openapi.json` |
| [`platform.get`](#operation-platform-get) | `GET /platform` |
| [`token.revoke`](#operation-token-revoke) | `POST /token/revoke` |
| [`users.get_chat`](#operation-users-get-chat) | `GET /users/{user_id}/chat` |
| [`users.list_profile_photos`](#operation-users-list-profile-photos) | `GET /users/{user_id}/profile_photos` |

## chats.get <a href="#operation-chats-get" id="operation-chats-get"></a>

`GET /bot-api/v1/chats/{chat_id}`

Read the metadata of a private chat your bot can access. Use the chat ID from an update or from `users.get_chat`, and keep it as a string.

This request reads an existing chat without starting it. Stopping a bot does not remove its chat. Deleted users and users removed from a test bot's tester list are no longer accessible.

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request" id="request"></a>

Do not send a request body.

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [ChatFullInfo](types/chatfullinfo.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

HTTP 404 `chat_not_found` means the chat is missing or is not visible to your bot. See [Receiving updates](../receiving-updates.md) to obtain chat IDs from conversations.

### Example <a href="#example" id="example"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
if (!arxBotToken || !chatId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.chatsGet(chatId);
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
    result = bot.chats_get(os.environ["CHAT_ID"])
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
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.chats_get(&chat_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "id": "4398046511200",
    "is_forum": false,
    "kind": "private",
    "user": {
      "has_photo": false,
      "id": "10000043",
      "is_bot": false,
      "name": "Ana López",
      "username": "ANALOPEZ"
    }
  }
}
```

## me.get <a href="#operation-me-get" id="operation-me-get"></a>

`GET /bot-api/v1/me`

Check that your token works and read your bot's identity. The result includes its handle, status fields, and feature availability.

Use feature values to decide which flows your bot should offer. The user ID in this response identifies your bot; a conversation's chat ID comes from an update or a chat lookup. Continue with [Authentication & tokens](../authentication.md) for token management.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-1" id="request-1"></a>

Do not send a request body.

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Bot](types/bot.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 403 `bot_suspended` means your bot cannot make API calls while suspended. The bot owner must resolve the suspension before it can resume.

### Example <a href="#example-1" id="example-1"></a>

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
  const result = await bot.meGet();
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
    result = bot.me_get()
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
    let result = bot.me_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/me" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "features": {
      "polling": "available"
    },
    "id": "10000042",
    "is_bot": true,
    "is_test": false,
    "name": "Acme Support",
    "official_role": null,
    "settings": {
      "can_join_groups": true
    },
    "username": "ACMESUPPORTBOT",
    "verification_status": "unverified"
  }
}
```

## openapi.get <a href="#operation-openapi-get" id="operation-openapi-get"></a>

`GET /bot-api/v1/openapi.json`

Download the OpenAPI document for the Bot API. You do not need a bot token for this request.

The response is the raw document, without the normal success envelope. Use it with an OpenAPI viewer or code generator. Return to the [API index](../api.md) for examples of each operation.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-2" id="request-2"></a>

Do not send a request body.

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body | Response headers |
| --- | --- | --- |
| 200 | [OpenApi](types/openapi.md) | `ETag`: Identifier of the current OpenAPI document. |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 503 `upstream_unavailable` means the document could not be retrieved because of a temporary service failure. Retry after the service recovers.

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
  const result = await bot.openapiGet();
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
    result = bot.openapi_get()
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
    let result = bot.openapi_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/openapi.json"
```

{% endtab %}

{% endtabs %}

The response is the complete OpenAPI JSON document, without an `ok` or `result` wrapper.

## platform.get <a href="#operation-platform-get" id="operation-platform-get"></a>

`GET /bot-api/v1/platform`

Read your bot's effective limits, sending profile, supported reactions, dice emoji, and message effects.

Use the returned values when pacing your bot. Sending intervals are integer microseconds. For a positive interval, divide 1,000,000 by the interval to obtain messages per second. The corresponding integer rate estimate rounds up and cannot express fractional rates. Burst values describe the allowed short burst.

A restricted sending profile still uses the normal HTTP 429 retry protocol. See [Limits](../limits.md) for the effective sending policy and size limits.

### Parameters <a href="#parameters-3" id="parameters-3"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-3" id="request-3"></a>

Do not send a request body.

### Response <a href="#response-3" id="response-3"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Platform](types/platform.md) in `result` |

### Errors <a href="#errors-3" id="errors-3"></a>

HTTP 400 `invalid_request` means you sent query parameters or a request body. Omit both when reading platform information.

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
  const result = await bot.platformGet();
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
    result = bot.platform_get()
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
    let result = bot.platform_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/platform" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "api_revision": "1",
    "dice_emoji": [
      "🎲",
      "🎯",
      "🎳",
      "🏀",
      "⚽",
      "🎰"
    ],
    "limits": {
      "global_send_burst": 120,
      "global_send_interval_microseconds": 33334,
      "global_send_rate_per_second": 30,
      "idempotency_receipt_seconds": 86400,
      "max_batch_bytes": 983040,
      "max_bot_commands": 100,
      "max_message_bytes": 786432,
      "max_page_content_bytes": 2031616,
      "max_polling_timeout_seconds": 50,
      "max_polling_updates": 100,
      "max_profile_localizations": 1000,
      "max_rich_blocks": 500,
      "max_rich_columns": 20,
      "max_rich_depth": 16,
      "max_rich_media": 50,
      "max_rich_utf16": 32768,
      "max_text_utf16": 4096,
      "private_send_burst": 5,
      "private_send_interval_microseconds": 1000000,
      "private_send_rate_per_second": 1
    },
    "message_effects": [
      {
        "asset_version": "1",
        "available_in": [
          "private"
        ],
        "effect_id": "aerx_thumbs_up",
        "emoji": "👍",
        "title": "Thumbs up"
      },
      {
        "asset_version": "1",
        "available_in": [
          "private"
        ],
        "effect_id": "aerx_heart",
        "emoji": "❤️",
        "title": "Heart"
      },
      {
        "asset_version": "1",
        "available_in": [
          "private"
        ],
        "effect_id": "aerx_fire",
        "emoji": "🔥",
        "title": "Fire"
      },
      {
        "asset_version": "1",
        "available_in": [
          "private"
        ],
        "effect_id": "aerx_party",
        "emoji": "🎉",
        "title": "Celebration"
      },
      {
        "asset_version": "1",
        "available_in": [
          "private"
        ],
        "effect_id": "aerx_poop",
        "emoji": "💩",
        "title": "Poop"
      },
      {
        "asset_version": "1",
        "available_in": [
          "private"
        ],
        "effect_id": "aerx_thumbs_down",
        "emoji": "👎",
        "title": "Thumbs down"
      }
    ],
    "reactions": {
      "allowed_emoji": [
        "👍",
        "👎",
        "❤️",
        "🔥",
        "🥰",
        "👏",
        "😁",
        "🤔",
        "🤯",
        "😱",
        "🤬",
        "😢",
        "🎉",
        "🤩",
        "🤮",
        "💩",
        "🙏",
        "👌",
        "🕊️",
        "🤡",
        "🥱",
        "🥴",
        "😍",
        "🐳",
        "❤️‍🔥",
        "🌚",
        "🌭",
        "💯",
        "🤣",
        "⚡",
        "🍌",
        "🏆",
        "💔",
        "🤨",
        "😐",
        "🍓",
        "🍾",
        "💋",
        "🖕",
        "😈",
        "😴",
        "😭",
        "🤓",
        "👻",
        "👨‍💻",
        "👀",
        "🎃",
        "🙈",
        "😇",
        "😨",
        "🤝",
        "✍️",
        "🤗",
        "🫡",
        "🎅",
        "🎄",
        "☃️",
        "💅",
        "🤪",
        "🗿",
        "🆒",
        "💘",
        "🙉",
        "🦄",
        "😘",
        "💊",
        "🙊",
        "😎",
        "👾",
        "🤷‍♂️",
        "🤷",
        "🤷‍♀️",
        "😡"
      ]
    },
    "revision": "1",
    "send_profile": "standard",
    "webhook_egress_cidrs": []
  }
}
```

## token.revoke <a href="#operation-token-revoke" id="operation-token-revoke"></a>

`POST /bot-api/v1/token/revoke`

Revoke the bot token used to make this request. Store a replacement securely before you resume your bot.

Requests admitted after revocation fail; a request already in progress may finish. Update delivery pauses. The bot owner can obtain a replacement through NexoCreator. The revoked token cannot authenticate another request, including a retry.

Follow [Security](../security.md) for the complete rotation sequence.

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
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-4" id="errors-4"></a>

HTTP 401 `unauthorized` means this token is already invalid or revoked. It cannot authenticate a retry; the bot owner must use NexoCreator to manage replacement tokens.

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
  const result = await bot.tokenRevoke({ key: requestKey });
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
    result = bot.token_revoke(key=os.environ["REQUEST_KEY"])
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
    let result = bot.token_revoke(&query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/token/revoke" \
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

## users.get_chat <a href="#operation-users-get-chat" id="operation-users-get-chat"></a>

`GET /bot-api/v1/users/{user_id}/chat`

Look up your bot's existing private chat with a known user. Pass the user's registration ID as a string, and use the returned chat ID for messaging.

This request does not create a chat or make an unknown user visible. A stopped bot retains its existing chat. Deleted accounts and users removed from a test bot's tester list are hidden.

### Parameters <a href="#parameters-5" id="parameters-5"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `user_id` | path | string | Yes | Use the user ID of the account available to your bot, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-5" id="request-5"></a>

Do not send a request body.

### Response <a href="#response-5" id="response-5"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [PrivateChat](types/privatechat.md) in `result` |

### Errors <a href="#errors-5" id="errors-5"></a>

HTTP 404 `chat_not_found` means the chat is missing or inaccessible to your bot. Continue with [Receiving updates](../receiving-updates.md) to obtain user and chat IDs from incoming updates.

### Example <a href="#example-5" id="example-5"></a>

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
  const result = await bot.usersGetChat(userId);
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
    result = bot.users_get_chat(os.environ["USER_ID"])
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
    let result = bot.users_get_chat(&user_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/users/${USER_ID}/chat" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "id": "4398046511200",
    "is_forum": false,
    "kind": "private",
    "user": {
      "has_photo": false,
      "id": "10000043",
      "is_bot": false,
      "name": "Ana López",
      "username": "ANALOPEZ"
    }
  }
}
```

## users.list_profile_photos <a href="#operation-users-list-profile-photos" id="operation-users-list-profile-photos"></a>

`GET /bot-api/v1/users/{user_id}/profile_photos`

Read the current profile photo of a user your bot is allowed to see.

The API returns zero or one current photo, with up to four sizes ordered from smallest to largest. There is no photo history, so `next_cursor` is null. Omit `cursor` or pass an empty string. A known user without a photo or sharing permission produces an empty result.

Users shared through a selection button must have allowed photo sharing. Test bots also enforce their tester access rules. Returned file IDs belong to your bot's library and use the normal file methods.

### Parameters <a href="#parameters-6" id="parameters-6"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `user_id` | path | string | Yes | Use the user ID of the account available to your bot, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `limit` | query | integer | No | Set the maximum number of results to return. A response may contain fewer results. The minimum is 1. The maximum is 100. The default is `100`. |
| `cursor` | query | string | No | Omit this parameter or leave it empty. Only the current profile photo is returned, so there is no continuation cursor. The maximum length is 0 characters. |

### Request <a href="#request-6" id="request-6"></a>

Do not send a request body.

### Response <a href="#response-6" id="response-6"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [UserProfilePhotos](types/userprofilephotos.md) in `result` |

### Errors <a href="#errors-6" id="errors-6"></a>

HTTP 404 `user_not_found` means your bot cannot access the user. HTTP 400 `invalid_parameter` with `field: cursor` means you should omit the cursor. See [Files](../files.md) for access and download rules.

### Example <a href="#example-6" id="example-6"></a>

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
  const result = await bot.usersListProfilePhotos(userId);
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
    result = bot.users_list_profile_photos(os.environ["USER_ID"])
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
    let result = bot.users_list_profile_photos(&user_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/users/${USER_ID}/profile_photos" \
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
    "next_cursor": null,
    "total_count": 0
  }
}
```

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
