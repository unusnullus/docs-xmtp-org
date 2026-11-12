# Groups and moderation

Read members, moderate supergroups, manage your bot's invite links, and answer join requests.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`members.list_admins`](#operation-members-list-admins) | `GET /chats/{chat_id}/administrators` |
| [`invite_links.create`](#operation-invite-links-create) | `POST /chats/{chat_id}/invite_links` |
| [`invite_links.export`](#operation-invite-links-export) | `POST /chats/{chat_id}/invite_links/export` |
| [`invite_links.edit`](#operation-invite-links-edit) | `PATCH /chats/{chat_id}/invite_links/{invite_code}` |
| [`invite_links.revoke`](#operation-invite-links-revoke) | `POST /chats/{chat_id}/invite_links/{invite_code}/revoke` |
| [`join_requests.approve`](#operation-join-requests-approve) | `POST /chats/{chat_id}/join_requests/{user_id}/approve` |
| [`join_requests.decline`](#operation-join-requests-decline) | `POST /chats/{chat_id}/join_requests/{user_id}/decline` |
| [`chats.leave`](#operation-chats-leave) | `POST /chats/{chat_id}/leave` |
| [`members.count`](#operation-members-count) | `GET /chats/{chat_id}/member_count` |
| [`members.get`](#operation-members-get) | `GET /chats/{chat_id}/members/{user_id}` |
| [`members.promote`](#operation-members-promote) | `PUT /chats/{chat_id}/members/{user_id}/admin` |
| [`members.unban`](#operation-members-unban) | `DELETE /chats/{chat_id}/members/{user_id}/ban` |
| [`members.ban`](#operation-members-ban) | `PUT /chats/{chat_id}/members/{user_id}/ban` |
| [`members.set_custom_title`](#operation-members-set-custom-title) | `PUT /chats/{chat_id}/members/{user_id}/custom_title` |
| [`members.restrict`](#operation-members-restrict) | `PUT /chats/{chat_id}/members/{user_id}/restriction` |
| [`chats.set_permissions`](#operation-chats-set-permissions) | `PUT /chats/{chat_id}/permissions` |
| [`chats.delete_photo`](#operation-chats-delete-photo) | `DELETE /chats/{chat_id}/photo` |
| [`chats.set_photo`](#operation-chats-set-photo) | `PUT /chats/{chat_id}/photo` |
| [`sender_chats.unban`](#operation-sender-chats-unban) | `DELETE /chats/{chat_id}/sender-chat-bans/{sender_chat_id}` |
| [`sender_chats.ban`](#operation-sender-chats-ban) | `PUT /chats/{chat_id}/sender-chat-bans/{sender_chat_id}` |
| [`join_request_queries.answer`](#operation-join-request-queries-answer) | `POST /join_request_queries/{query_id}/answer` |
| [`join_request_queries.open_mini_app`](#operation-join-request-queries-open-mini-app) | `POST /join_request_queries/{query_id}/open_mini_app` |

## members.list_admins <a href="#operation-members-list-admins" id="operation-members-list-admins"></a>

`GET /bot-api/v1/chats/{chat_id}/administrators`

List the owner and current administrators of a Super Group. Your bot must be a current member; it needs no administrator right.

By default the list contains people only. Set `include_bots=true` to include other bot administrators. Your own bot appears whenever it is an administrator. Each entry is a member object with `status` `owner` or `administrator`; administrator entries also carry their rights and `can_be_edited`, which tells you whether your bot may change that administrator.

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `include_bots` | query | boolean | No | Include other bot administrators. The calling bot is included when it is an administrator, including when this parameter is false. The default is `false`. |

### Request <a href="#request" id="request"></a>

Do not send a request body.

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [GroupAdministrators](types/groupadministrators.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. HTTP 400 `invalid_request` means the query contains a parameter other than `include_bots`, or a value other than `true` or `false`. See [Errors](../errors.md) for the shared error format.

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
  const result = await bot.membersListAdmins(chatId);
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
    result = bot.members_list_admins(os.environ["CHAT_ID"])
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
    let result = bot.members_list_admins(&chat_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/administrators" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": [
    {
      "is_anonymous": false,
      "status": "owner",
      "user": {
        "has_photo": false,
        "id": "700001234",
        "is_bot": false,
        "name": "Ana López",
        "username": "ANALOPEZ"
      }
    }
  ]
}
```

## invite_links.create <a href="#operation-invite-links-create" id="operation-invite-links-create"></a>

`POST /bot-api/v1/chats/{chat_id}/invite_links`

Create an additional invite link for a Super Group. Your bot must be an administrator with `can_invite_users`, and it owns the links it creates.

All fields are optional. `name` is a label of up to 32 characters without control characters. `expires_at` must be in the future. `member_limit` caps how many people who joined through the link can be members at the same time, from 1 to 99999. `creates_join_request` defaults to `false`; set it to `true` to make people who open the link send a join request instead of joining directly. A link with `creates_join_request: true` cannot also set `member_limit`. Your bot can hold up to 1000 links per group that are not revoked; expired links count until you revoke them. The result is the new link, including `invite_link` and the `invite_code` you pass to edit or revoke it.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-1" id="request-1"></a>

Content type: `application/json`. You may omit the body.

[GroupCreateInviteLink](types/groupcreateinvitelink.md)

Arguments for invite_links.create in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation. Only the listed fields are allowed. Omit `member_limit`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `creates_join_request` | boolean | No | Whether this invitation creates join requests. It cannot be combined with a member limit. The default is `false`. |
| `expires_at` | string | No | The invitation expiration time in RFC 3339 format; omitted for no fixed expiration. The format is `date-time`. |
| `member_limit` | integer | No | The maximum number of concurrent members admitted by this invitation, from 1 to 99999. |
| `name` | string | No | The invitation's optional display label. The maximum length is 32 characters. |

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [GroupInviteLink](types/groupinvitelink.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_invite_users`. HTTP 400 `invalid_request` means a field is out of range, `expires_at` is not in the future, or the combination is not allowed. HTTP 409 `invite_link_limit_reached` means your bot already holds 1000 unrevoked links in this group; revoke unused links first. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. See [`invite_links.revoke`](groups.md#operation-invite-links-revoke).

### Example <a href="#example-1" id="example-1"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.GroupCreateInviteLink = {
    "name": "Support desk",
    "creates_join_request": false
  };
  const result = await bot.inviteLinksCreate(chatId, { body, key: requestKey });
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
    body = models.GroupCreateInviteLink(name='Support desk', creates_join_request=False)
    result = bot.invite_links_create(os.environ["CHAT_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::GroupCreateInviteLink {
        creates_join_request: Some(false),
        name: Some("Support desk".to_owned()),
        ..Default::default()
    };
    let query = [];
    let result = bot.invite_links_create(&chat_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/invite_links" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"name": "Support desk", "creates_join_request": false}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "creates_join_request": false,
    "creator": {
      "has_photo": false,
      "id": "700001235",
      "is_bot": true,
      "name": "Acme Support",
      "username": "ACMESUPPORTBOT"
    },
    "invite_code": "abcdefghijklmnopqrstuv",
    "invite_link": "https://www.arx.pro/join/abcdefghijklmnopqrstuv",
    "is_primary": false,
    "is_revoked": false
  }
}
```

## invite_links.export <a href="#operation-invite-links-export" id="operation-invite-links-export"></a>

`POST /bot-api/v1/chats/{chat_id}/invite_links/export`

Generate a new primary invite link for your bot in a Super Group and return its URL. Your bot must be an administrator with `can_invite_users`.

Each bot has its own primary link per group. Exporting revokes your bot's current primary link, if any, and creates its replacement in one step, so the old URL stops working at once. Links created by other administrators are not affected. The result is the new URL as a string. Store it: `chats.get` does not return your bot's invite link. Reuse the same `Idempotency-Key` after a lost response to receive the same URL instead of replacing the link again.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-2" id="request-2"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [GroupInviteUrl](types/groupinviteurl.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_invite_users`. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. HTTP 429 `join_rate_limited` means your bot made too many group changes in a short time; wait for `retry_after` seconds. See [`invite_links.create`](groups.md#operation-invite-links-create) for additional links.

### Example <a href="#example-2" id="example-2"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.inviteLinksExport(chatId, { key: requestKey });
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
    result = bot.invite_links_export(os.environ["CHAT_ID"], key=os.environ["REQUEST_KEY"])
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
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.invite_links_export(&chat_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/invite_links/export" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": "https://www.arx.pro/join/abcdefghijklmnopqrstuv"
}
```

## invite_links.edit <a href="#operation-invite-links-edit" id="operation-invite-links-edit"></a>

`PATCH /bot-api/v1/chats/{chat_id}/invite_links/{invite_code}`

Change the settings of an invite link your bot created. Your bot must be an administrator with `can_invite_users`. Identify the link by its `invite_code`.

Send only the fields you want to change: `name`, `expires_at`, `member_limit` or `creates_join_request`. An omitted field keeps its value, an empty `name` clears the label, and `null` is rejected. A new `expires_at` must be in the future. The rules apply to the combined result, so a link cannot end up with both `creates_join_request: true` and a `member_limit`. You cannot edit your bot's primary link or a revoked link. The result is the updated link.

### Parameters <a href="#parameters-3" id="parameters-3"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `invite_code` | path | string | Yes | Use the invite_code of the exact link owned by your bot. The value must match `^[A-Za-z0-9_-]{22}$`. The minimum length is 22 characters. The maximum length is 22 characters. |

### Request <a href="#request-3" id="request-3"></a>

Content type: `application/json`. You must include the body.

[GroupEditInviteLink](types/groupeditinvitelink.md)

Arguments for invite_links.edit in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation. Only the listed fields are allowed. Do not supply `member_limit` and `creates_join_request` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `creates_join_request` | boolean | No | Whether this invitation creates join requests. It cannot be combined with a member limit. The default is `false`. |
| `expires_at` | string | No | The invitation expiration time in RFC 3339 format; omitted for no fixed expiration. The format is `date-time`. |
| `member_limit` | integer | No | The maximum number of concurrent members admitted by this invitation, from 1 to 99999. |
| `name` | string | No | The invitation's optional display label. The maximum length is 32 characters. |

### Response <a href="#response-3" id="response-3"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [GroupInviteLink](types/groupinvitelink.md) in `result` |

### Errors <a href="#errors-3" id="errors-3"></a>

HTTP 403 `invite_link_not_editable` means another administrator created the link, or it is your primary link. HTTP 404 `invite_link_invalid` means the code is unknown or the link is revoked. HTTP 400 `invalid_request` means a field is invalid or the combined settings conflict. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. Use [`invite_links.export`](groups.md#operation-invite-links-export) to replace a primary link.

### Example <a href="#example-3" id="example-3"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `INVITE_CODE`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const inviteCode = process.env.INVITE_CODE;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !inviteCode || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, INVITE_CODE, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.GroupEditInviteLink = {
    "name": ""
  };
  const result = await bot.inviteLinksEdit(chatId, inviteCode, { body, key: requestKey });
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
    body = models.GroupEditInviteLink(name='')
    result = bot.invite_links_edit(os.environ["CHAT_ID"], os.environ["INVITE_CODE"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let invite_code = std::env::var("INVITE_CODE")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::GroupEditInviteLink {
        name: Some("".to_owned()),
        ..Default::default()
    };
    let query = [];
    let result = bot.invite_links_edit(&chat_id, &invite_code, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PATCH \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/invite_links/${INVITE_CODE}" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"name": ""}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "creates_join_request": false,
    "creator": {
      "has_photo": false,
      "id": "700001235",
      "is_bot": true,
      "name": "Acme Support",
      "username": "ACMESUPPORTBOT"
    },
    "invite_code": "abcdefghijklmnopqrstuv",
    "invite_link": "https://www.arx.pro/join/abcdefghijklmnopqrstuv",
    "is_primary": false,
    "is_revoked": false
  }
}
```

## invite_links.revoke <a href="#operation-invite-links-revoke" id="operation-invite-links-revoke"></a>

`POST /bot-api/v1/chats/{chat_id}/invite_links/{invite_code}/revoke`

Revoke an invite link your bot created so nobody can join through it again. Your bot must be an administrator with `can_invite_users`. Identify the link by its `invite_code`.

The result is the revoked link with `is_revoked: true`. If you revoke your bot's primary link, a new primary link replaces it in the same step, but the result does not include the new URL. To get a usable primary URL, call [`invite_links.export`](groups.md#operation-invite-links-export); it replaces the primary link again and returns the new URL. Revoking a link that is already revoked returns it without further changes.

### Parameters <a href="#parameters-4" id="parameters-4"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `invite_code` | path | string | Yes | Use the invite_code of the exact link owned by your bot. The value must match `^[A-Za-z0-9_-]{22}$`. The minimum length is 22 characters. The maximum length is 22 characters. |

### Request <a href="#request-4" id="request-4"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-4" id="response-4"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [GroupInviteLink](types/groupinvitelink.md) in `result` |

### Errors <a href="#errors-4" id="errors-4"></a>

HTTP 403 `invite_link_not_editable` means another administrator created the link. HTTP 404 `invite_link_invalid` means the code does not match a link in this group. HTTP 403 `not_enough_rights` means your bot lacks `can_invite_users`. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. See [Errors](../errors.md) for the shared error format.

### Example <a href="#example-4" id="example-4"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `INVITE_CODE`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const inviteCode = process.env.INVITE_CODE;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !inviteCode || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, INVITE_CODE, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.inviteLinksRevoke(chatId, inviteCode, { key: requestKey });
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
    result = bot.invite_links_revoke(os.environ["CHAT_ID"], os.environ["INVITE_CODE"], key=os.environ["REQUEST_KEY"])
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
    let invite_code = std::env::var("INVITE_CODE")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.invite_links_revoke(&chat_id, &invite_code, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/invite_links/${INVITE_CODE}/revoke" \
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
    "creates_join_request": false,
    "creator": {
      "has_photo": false,
      "id": "700001235",
      "is_bot": true,
      "name": "Acme Support",
      "username": "ACMESUPPORTBOT"
    },
    "invite_code": "abcdefghijklmnopqrstuv",
    "invite_link": "https://www.arx.pro/join/abcdefghijklmnopqrstuv",
    "is_primary": false,
    "is_revoked": true
  }
}
```

## join_requests.approve <a href="#operation-join-requests-approve" id="operation-join-requests-approve"></a>

`POST /bot-api/v1/chats/{chat_id}/join_requests/{user_id}/approve`

Approve the pending join request of a person, identified by `user_id`. Your bot must be an administrator with `can_invite_users`.

The request adds the person to the group, and members see a join service message. Your bot receives join requests as `chat_join_request` updates. Only the person's current pending request is approved; if it was already decided, cancelled or expired, the call fails instead of acting on a later request. When the request came through an invite link, that link must still be usable and within its `member_limit`. Retry a lost response with the same `Idempotency-Key`.

### Parameters <a href="#parameters-5" id="parameters-5"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `user_id` | path | string | Yes | Use the user ID of the account available to your bot, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-5" id="request-5"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-5" id="response-5"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-5" id="errors-5"></a>

HTTP 404 `join_request_not_found` means the person has no join request in this group. HTTP 409 `join_request_already_processed` means another decision won, the request expired, or the person is already a member. HTTP 404 `invite_link_invalid` means the request's link was revoked, and HTTP 409 `invite_link_limit_reached` means that link reached its `member_limit`. HTTP 409 `groups_per_account_limit_reached` means the person is in too many groups. HTTP 403 `access_restricted` means the person's account cannot join this group. HTTP 403 `not_enough_rights` means your bot lacks `can_invite_users`. HTTP 409 `group_member_limit_reached` means the group is full. HTTP 410 `invite_link_expired` means the request's link expired. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. See [Updates](../updates.md) for join request delivery.

### Example <a href="#example-5" id="example-5"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `USER_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const userId = process.env.USER_ID;
if (!arxBotToken || !chatId || !requestKey || !userId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, USER_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.joinRequestsApprove(chatId, userId, { key: requestKey });
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
    result = bot.join_requests_approve(os.environ["CHAT_ID"], os.environ["USER_ID"], key=os.environ["REQUEST_KEY"])
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
    let request_key = std::env::var("REQUEST_KEY")?;
    let user_id = std::env::var("USER_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.join_requests_approve(&chat_id, &user_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/join_requests/${USER_ID}/approve" \
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

## join_requests.decline <a href="#operation-join-requests-decline" id="operation-join-requests-decline"></a>

`POST /bot-api/v1/chats/{chat_id}/join_requests/{user_id}/decline`

Decline the pending join request of a person, identified by `user_id`. Your bot must be an administrator with `can_invite_users`.

Declining does not ban the person. The call acts only on the current pending request. If you retry with the same `Idempotency-Key` and body, the accepted result replays and never declines a newer request from the same person. Use [`members.ban`](groups.md#operation-members-ban) when you want to keep someone out.

### Parameters <a href="#parameters-6" id="parameters-6"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `user_id` | path | string | Yes | Use the user ID of the account available to your bot, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-6" id="request-6"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-6" id="response-6"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-6" id="errors-6"></a>

HTTP 404 `join_request_not_found` means the person has no join request in this group. HTTP 409 `join_request_already_processed` means another decision won or the request expired. HTTP 403 `not_enough_rights` means your bot lacks `can_invite_users`. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. See [Updates](../updates.md) for join request delivery.

### Example <a href="#example-6" id="example-6"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `USER_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const userId = process.env.USER_ID;
if (!arxBotToken || !chatId || !requestKey || !userId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, USER_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.joinRequestsDecline(chatId, userId, { key: requestKey });
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
    result = bot.join_requests_decline(os.environ["CHAT_ID"], os.environ["USER_ID"], key=os.environ["REQUEST_KEY"])
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
    let request_key = std::env::var("REQUEST_KEY")?;
    let user_id = std::env::var("USER_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.join_requests_decline(&chat_id, &user_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/join_requests/${USER_ID}/decline" \
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

## chats.leave <a href="#operation-chats-leave" id="operation-chats-leave"></a>

`POST /bot-api/v1/chats/{chat_id}/leave`

Remove your bot from a Super Group. Your bot needs no administrator right to leave.

After it leaves, your bot is no longer a member and loses any administrator rights and custom title it held. If the response is lost, retry with the same `Idempotency-Key` and an empty body. A completed departure replays as `true` and never removes a newer membership created after it.

### Parameters <a href="#parameters-7" id="parameters-7"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-7" id="request-7"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-7" id="response-7"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-7" id="errors-7"></a>

HTTP 404 `chat_not_found` means the group does not exist or your bot is not a current member. HTTP 403 `capability_not_available` means group moderation is not available to your bot. HTTP 409 `idempotency_key_reused` means the key was used for a different request. See [Making requests](../making-requests.md#idempotency) for key rules.

### Example <a href="#example-7" id="example-7"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.chatsLeave(chatId, { key: requestKey });
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
    result = bot.chats_leave(os.environ["CHAT_ID"], key=os.environ["REQUEST_KEY"])
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
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.chats_leave(&chat_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/leave" \
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

## members.count <a href="#operation-members-count" id="operation-members-count"></a>

`GET /bot-api/v1/chats/{chat_id}/member_count`

Read the current number of members in a Super Group. Your bot must be a current member; it needs no administrator right.

The result is a single non-negative integer that reflects the group at the time of the request. This is a read, so you can repeat it freely.

### Parameters <a href="#parameters-8" id="parameters-8"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-8" id="request-8"></a>

Do not send a request body.

### Response <a href="#response-8" id="response-8"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [GroupMemberCount](types/groupmembercount.md) in `result` |

### Errors <a href="#errors-8" id="errors-8"></a>

HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. HTTP 403 `capability_not_available` means group moderation is not available to your bot. See [Errors](../errors.md) for the shared error format.

### Example <a href="#example-8" id="example-8"></a>

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
  const result = await bot.membersCount(chatId);
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
    result = bot.members_count(os.environ["CHAT_ID"])
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
    let result = bot.members_count(&chat_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/member_count" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": 12
}
```

## members.get <a href="#operation-members-get" id="operation-members-get"></a>

`GET /bot-api/v1/chats/{chat_id}/members/{user_id}`

Read one member of a Super Group. Pass the user's decimal ID, or `@username` for an exact, case-insensitive handle match.

Your bot can always read its own membership. Reading another user requires your bot to be a current administrator; no specific right is needed. The result reports `status` (`owner`, `administrator`, `member`, `restricted`, `left` or `banned`) with the fields that apply: administrator rights and `can_be_edited`, effective permissions for a restricted member, and `until_at` for a temporary sanction. Resolving a username returns the canonical user ID and grants no extra authority.

### Parameters <a href="#parameters-9" id="parameters-9"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `user_id` | path | string | Yes | Use the canonical decimal user ID or an exact @username (8 through 24 ASCII letters/digits, case-insensitive). Resolution preserves the current membership-read permissions; the result contains the canonical user ID. The value must match `^([1-9][0-9]*\|@[A-Za-z0-9]{8,24})$`. |

### Request <a href="#request-9" id="request-9"></a>

Do not send a request body.

### Response <a href="#response-9" id="response-9"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [GroupMember](types/groupmember.md) in `result` |

### Errors <a href="#errors-9" id="errors-9"></a>

HTTP 403 `not_enough_rights` means your bot is not an administrator and asked for another user. HTTP 404 `chat_not_found` means the group, your bot's membership or the member record is not visible. HTTP 400 `invalid_request` means the user ID or username is malformed. HTTP 403 `user_deactivated` means the account is no longer active. See [`members.list_admins`](groups.md#operation-members-list-admins) to read all administrators at once.

### Example <a href="#example-9" id="example-9"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `USER_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const userId = process.env.USER_ID;
if (!arxBotToken || !chatId || !userId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, USER_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.membersGet(chatId, userId);
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
    result = bot.members_get(os.environ["CHAT_ID"], os.environ["USER_ID"])
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
    let user_id = std::env::var("USER_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.members_get(&chat_id, &user_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/members/${USER_ID}" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "status": "member",
    "user": {
      "has_photo": false,
      "id": "700001234",
      "is_bot": false,
      "name": "Ana López",
      "username": "ANALOPEZ"
    }
  }
}
```

## members.promote <a href="#operation-members-promote" id="operation-members-promote"></a>

`PUT /bot-api/v1/chats/{chat_id}/members/{user_id}/admin`

Promote a human member to administrator, change an administrator's rights, or demote one. Your bot must be an administrator with `can_promote_members`.

The `rights` object replaces the target's rights. Any right you omit is `false`, and `rights: {}` demotes the target to a regular member. Your bot can grant only rights it holds itself. It can edit only administrators in its own promotion chain, and never the owner, itself or another bot. The target must be a current member. A group has at most 50 administrators. The result is `true`; an unchanged result has no new effects.

### Parameters <a href="#parameters-10" id="parameters-10"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `user_id` | path | string | Yes | Use the user ID of the account available to your bot, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-10" id="request-10"></a>

Content type: `application/json`. You must include the body.

[GroupPromoteMember](types/grouppromotemember.md)

Arguments for members.promote in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `rights` | [GroupAdminRights](types/groupadminrights.md) | Yes | These are the default administrator permissions that your bot requests when added to a chat. |

### Response <a href="#response-10" id="response-10"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-10" id="errors-10"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_promote_members`, asked for a right it does not hold, targeted an administrator it cannot edit, or the target is no longer a member. HTTP 400 `invalid_rights` means `rights` names an unknown right or the group already has 50 administrators. HTTP 403 `capability_not_available` means the target is a bot. HTTP 404 `chat_not_found` means the target has no membership record in this group. See [`members.set_custom_title`](groups.md#operation-members-set-custom-title) to label an administrator.

### Example <a href="#example-10" id="example-10"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `USER_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const userId = process.env.USER_ID;
if (!arxBotToken || !chatId || !requestKey || !userId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, USER_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.GroupPromoteMember = {
    "rights": {
      "can_restrict_members": true
    }
  };
  const result = await bot.membersPromote(chatId, userId, { body, key: requestKey });
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
    body = models.GroupPromoteMember(rights=models.GroupAdminRights(can_restrict_members=True))
    result = bot.members_promote(os.environ["CHAT_ID"], os.environ["USER_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let user_id = std::env::var("USER_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::GroupPromoteMember::new(
        models::GroupAdminRights {
            can_restrict_members: Some(true),
            ..Default::default()
        },
    );
    let query = [];
    let result = bot.members_promote(&chat_id, &user_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/members/${USER_ID}/admin" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"rights": {"can_restrict_members": true}}'
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

## members.unban <a href="#operation-members-unban" id="operation-members-unban"></a>

`DELETE /bot-api/v1/chats/{chat_id}/members/{user_id}/ban`

Lift a ban, or remove a member from a Super Group without banning them. Your bot must be an administrator with `can_restrict_members`.

By default, the request removes the user if they are present and leaves them free to rejoin; use this to kick a member. With `only_if_banned: true`, the request changes only a current ban and leaves members untouched. This operation never adds the user back to the group. When the user has no membership record, the request returns `true` without effects. You cannot remove the owner, your bot, another bot, or an administrator outside your bot's promotion chain.

### Parameters <a href="#parameters-11" id="parameters-11"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `user_id` | path | string | Yes | Use the user ID of the account available to your bot, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-11" id="request-11"></a>

Content type: `application/json`. You may omit the body.

[GroupUnbanMember](types/groupunbanmember.md)

Arguments for members.unban in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `only_if_banned` | boolean | No | Whether to unban only an account that is currently banned. The default is `false`. |

### Response <a href="#response-11" id="response-11"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-11" id="errors-11"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_restrict_members` or cannot act on this target. HTTP 403 `capability_not_available` means the target is a bot. HTTP 429 `join_rate_limited` means your bot made too many group changes in a short time; wait for `retry_after` seconds. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. Use [`members.ban`](groups.md#operation-members-ban) to keep a user out.

### Example <a href="#example-11" id="example-11"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `USER_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const userId = process.env.USER_ID;
if (!arxBotToken || !chatId || !requestKey || !userId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, USER_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.GroupUnbanMember = {
    "only_if_banned": true
  };
  const result = await bot.membersUnban(chatId, userId, { body, key: requestKey });
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
    body = models.GroupUnbanMember(only_if_banned=True)
    result = bot.members_unban(os.environ["CHAT_ID"], os.environ["USER_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let user_id = std::env::var("USER_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::GroupUnbanMember {
        only_if_banned: Some(true),
        ..Default::default()
    };
    let query = [];
    let result = bot.members_unban(&chat_id, &user_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X DELETE \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/members/${USER_ID}/ban" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"only_if_banned": true}'
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

## members.ban <a href="#operation-members-ban" id="operation-members-ban"></a>

`PUT /bot-api/v1/chats/{chat_id}/members/{user_id}/ban`

Remove a human from a Super Group and prevent them from rejoining. Your bot must be an administrator with `can_restrict_members`.

The ban removes the user's access to the group and its history. Messages and content they already shared stay in the group. `revoke_messages` has the effective value `true` in Super Groups whatever you send. Set `until_at` for a temporary ban; an omitted value, or one less than 30 seconds or more than 366 days away, makes the ban permanent. You can ban a user who has never joined. A pending join request from the user is cancelled. You cannot ban the owner, your bot, another bot, or an administrator outside your bot's promotion chain.

### Parameters <a href="#parameters-12" id="parameters-12"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `user_id` | path | string | Yes | Use the user ID of the account available to your bot, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-12" id="request-12"></a>

Content type: `application/json`. You may omit the body.

[GroupBanMember](types/groupbanmember.md)

Arguments for members.ban in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `revoke_messages` | boolean | No | Revokes access to the group and its server history. In Super Groups either boolean has the effective value true; messages and shared content are retained. The default is `true`. |
| `until_at` | string | No | The membership, restriction or ban expiration time in RFC 3339 format; omitted when indefinite. The format is `date-time`. |

### Response <a href="#response-12" id="response-12"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-12" id="errors-12"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_restrict_members` or cannot act on this target. HTTP 403 `capability_not_available` means the target is a bot. HTTP 403 `user_deactivated` means the account is no longer active. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. Use [`members.unban`](groups.md#operation-members-unban) to lift the ban.

### Example <a href="#example-12" id="example-12"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `USER_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const userId = process.env.USER_ID;
if (!arxBotToken || !chatId || !requestKey || !userId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, USER_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.GroupBanMember = {};
  const result = await bot.membersBan(chatId, userId, { body, key: requestKey });
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
    body = models.GroupBanMember()
    result = bot.members_ban(os.environ["CHAT_ID"], os.environ["USER_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let user_id = std::env::var("USER_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::GroupBanMember::default();
    let query = [];
    let result = bot.members_ban(&chat_id, &user_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/members/${USER_ID}/ban" \
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
  "result": true
}
```

## members.set_custom_title <a href="#operation-members-set-custom-title" id="operation-members-set-custom-title"></a>

`PUT /bot-api/v1/chats/{chat_id}/members/{user_id}/custom_title`

Set or clear the custom title shown for an administrator. Your bot must be an administrator with `can_promote_members`.

The target must be a human administrator that your bot promoted directly with [`members.promote`](groups.md#operation-members-promote). The title holds at most 16 characters (Unicode scalar values) and cannot contain emoji or control characters. Send an empty `custom_title` to clear it. The result is `true`; setting the current title again has no new effects.

### Parameters <a href="#parameters-13" id="parameters-13"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `user_id` | path | string | Yes | Use the user ID of the account available to your bot, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-13" id="request-13"></a>

Content type: `application/json`. You must include the body.

[GroupSetCustomTitle](types/groupsetcustomtitle.md)

Arguments for members.set_custom_title in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `custom_title` | string | Yes | The administrator's displayed title, up to 16 characters. |

### Response <a href="#response-13" id="response-13"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-13" id="errors-13"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_promote_members`, or the target is not an administrator your bot promoted. HTTP 404 `chat_not_found` means the target has no membership record in this group. HTTP 403 `capability_not_available` means the target is a bot. HTTP 400 `invalid_request` means the title is too long or contains emoji or control characters. See [Errors](../errors.md) for the shared error format.

### Example <a href="#example-13" id="example-13"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `USER_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const userId = process.env.USER_ID;
if (!arxBotToken || !chatId || !requestKey || !userId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, USER_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.GroupSetCustomTitle = {
    "custom_title": "Moderator"
  };
  const result = await bot.membersSetCustomTitle(chatId, userId, { body, key: requestKey });
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
    body = models.GroupSetCustomTitle(custom_title='Moderator')
    result = bot.members_set_custom_title(os.environ["CHAT_ID"], os.environ["USER_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let user_id = std::env::var("USER_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::GroupSetCustomTitle::new("Moderator".to_owned());
    let query = [];
    let result = bot.members_set_custom_title(&chat_id, &user_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/members/${USER_ID}/custom_title" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"custom_title": "Moderator"}'
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

## members.restrict <a href="#operation-members-restrict" id="operation-members-restrict"></a>

`PUT /bot-api/v1/chats/{chat_id}/members/{user_id}/restriction`

Replace the restriction on a human member of a Super Group. Your bot must be an administrator with `can_restrict_members`.

The `permissions` object replaces the member's permissions; omitted fields are `false`, with the same fallbacks as [`chats.set_permissions`](groups.md#operation-chats-set-permissions). The effective permissions never exceed the group defaults. Set `until_at` to lift the restriction automatically. An omitted `until_at`, or one less than 30 seconds or more than 366 days away, makes the restriction permanent. You can restrict a user before they join. Granting every permission lifts the restriction. You cannot restrict the owner, your bot, another bot, or an administrator outside your bot's promotion chain.

### Parameters <a href="#parameters-14" id="parameters-14"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `user_id` | path | string | Yes | Use the user ID of the account available to your bot, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-14" id="request-14"></a>

Content type: `application/json`. You must include the body.

[GroupRestrictMember](types/grouprestrictmember.md)

Arguments for members.restrict in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `permissions` | [GroupPermissions](types/grouppermissions.md) | Yes | The group defaults or member permission values. Effective member permissions intersect the applicable group defaults. |
| `until_at` | string | No | The membership, restriction or ban expiration time in RFC 3339 format; omitted when indefinite. The format is `date-time`. |
| `use_independent_chat_permissions` | boolean | No | Whether to preserve independently supplied permission values instead of applying the documented implication rules. The default is `false`. |

### Response <a href="#response-14" id="response-14"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-14" id="errors-14"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_restrict_members` or cannot act on this target. HTTP 403 `capability_not_available` means the target is a bot. HTTP 403 `user_banned_from_chat` means the user is banned; lift the ban first. HTTP 400 `invalid_request` means a permission field or `until_at` is invalid. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. See [`members.unban`](groups.md#operation-members-unban) to lift a ban.

### Example <a href="#example-14" id="example-14"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `USER_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const userId = process.env.USER_ID;
if (!arxBotToken || !chatId || !requestKey || !userId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, USER_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.GroupRestrictMember = {
    "permissions": {
      "can_send_messages": false
    },
    "use_independent_chat_permissions": true
  };
  const result = await bot.membersRestrict(chatId, userId, { body, key: requestKey });
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
    body = models.GroupRestrictMember(
        permissions=models.GroupPermissions(can_send_messages=False),
        use_independent_chat_permissions=True,
    )
    result = bot.members_restrict(os.environ["CHAT_ID"], os.environ["USER_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let user_id = std::env::var("USER_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::GroupRestrictMember {
        use_independent_chat_permissions: Some(true),
        ..models::GroupRestrictMember::new(
            models::GroupPermissions {
                can_send_messages: Some(false),
                ..Default::default()
            },
        )
    };
    let query = [];
    let result = bot.members_restrict(&chat_id, &user_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/members/${USER_ID}/restriction" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"permissions": {"can_send_messages": false}, "use_independent_chat_permissions": true}'
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

## chats.set_permissions <a href="#operation-chats-set-permissions" id="operation-chats-set-permissions"></a>

`PUT /bot-api/v1/chats/{chat_id}/permissions`

Replace the default permissions that apply to every member of a Super Group. Your bot must be an administrator with `can_restrict_members`.

The `permissions` object replaces the whole set. A permission you omit is `false`, with three exceptions: `can_react_to_messages` follows `can_send_messages`, and `can_edit_tag` and `can_manage_topics` follow `can_pin_messages`. Unless you set `use_independent_chat_permissions` to `true`, the server applies permission implications before saving. A request that leaves the normalized set unchanged returns `true` without new effects. Member restrictions from [`members.restrict`](groups.md#operation-members-restrict) can only narrow these defaults.

### Parameters <a href="#parameters-15" id="parameters-15"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-15" id="request-15"></a>

Content type: `application/json`. You must include the body.

[GroupSetPermissions](types/groupsetpermissions.md)

Arguments for chats.set_permissions in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `permissions` | [GroupPermissions](types/grouppermissions.md) | Yes | The group defaults or member permission values. Effective member permissions intersect the applicable group defaults. |
| `use_independent_chat_permissions` | boolean | No | Whether to preserve independently supplied permission values instead of applying the documented implication rules. The default is `false`. |

### Response <a href="#response-15" id="response-15"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-15" id="errors-15"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_restrict_members`. HTTP 400 `invalid_request` means `permissions` contains an unknown field or a non-boolean value. HTTP 429 `join_rate_limited` means your bot made too many group changes in a short time; wait for `retry_after` seconds. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. See [Errors](../errors.md) for retry rules.

### Example <a href="#example-15" id="example-15"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.GroupSetPermissions = {
    "permissions": {
      "can_send_messages": true
    }
  };
  const result = await bot.chatsSetPermissions(chatId, { body, key: requestKey });
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
    body = models.GroupSetPermissions(permissions=models.GroupPermissions(can_send_messages=True))
    result = bot.chats_set_permissions(os.environ["CHAT_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::GroupSetPermissions::new(
        models::GroupPermissions {
            can_send_messages: Some(true),
            ..Default::default()
        },
    );
    let query = [];
    let result = bot.chats_set_permissions(&chat_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/permissions" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"permissions": {"can_send_messages": true}}'
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

## chats.delete_photo <a href="#operation-chats-delete-photo" id="operation-chats-delete-photo"></a>

`DELETE /bot-api/v1/chats/{chat_id}/photo`

Remove the photo of a Super Group. Your bot must be an administrator with `can_change_info`.

Members see a service message when the photo is removed. When the group has no photo, the request returns `true` without new effects, so you can repeat it safely. Send an empty JSON object as the body.

### Parameters <a href="#parameters-16" id="parameters-16"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-16" id="request-16"></a>

Content type: `application/json`. You must include the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-16" id="response-16"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-16" id="errors-16"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_change_info`. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. HTTP 429 `join_rate_limited` means your bot made too many group changes in a short time; wait for `retry_after` seconds. See [Errors](../errors.md) for the shared error format.

### Example <a href="#example-16" id="example-16"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.chatsDeletePhoto(chatId, { key: requestKey });
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
    result = bot.chats_delete_photo(os.environ["CHAT_ID"], key=os.environ["REQUEST_KEY"])
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
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.chats_delete_photo(&chat_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X DELETE \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/photo" \
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

## chats.set_photo <a href="#operation-chats-set-photo" id="operation-chats-set-photo"></a>

`PUT /bot-api/v1/chats/{chat_id}/photo`

Replace the photo of a Super Group. Your bot must be an administrator with `can_change_info`.

Pass `photo` as an object with `file_id` (a file your bot can use) or `url`. The file must be a JPEG, PNG or WebP image of at most 10 MiB. Members see a service message, and the new photo appears in [`chats.get`](platform.md#operation-chats-get). Setting the photo the group already has returns `true` without new effects. A retry with the same `Idempotency-Key` and body returns the original result.

### Parameters <a href="#parameters-17" id="parameters-17"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-17" id="request-17"></a>

Content type: `application/json`. You must include the body.

[GroupSetChatPhoto](types/groupsetchatphoto.md)

Arguments for chats.set_photo in Super Groups with group moderation enabled. Current membership, permissions and promotion ancestry control the operation. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `photo` | [InputFile](types/inputfile.md) | Yes | This contains the photo sizes or photo source for the object. |

### Response <a href="#response-17" id="response-17"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-17" id="errors-17"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_change_info`. HTTP 400 `file_type_mismatch` means the file is not a supported photo, and HTTP 413 `file_too_large` means it exceeds 10 MiB. HTTP 409 `file_not_ready` means the upload is still processing; wait and retry. HTTP 404 `file_not_found` means the `file_id` is unknown to your bot. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. See [Files](../files.md) for uploads and file IDs.

### Example <a href="#example-17" id="example-17"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `FILE_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models, FileInput } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const fileId = process.env.FILE_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !fileId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, FILE_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.GroupSetChatPhoto = {
    "photo": FileInput.fromId(fileId)
  };
  const result = await bot.chatsSetPhoto(chatId, { body, key: requestKey });
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
    body = models.GroupSetChatPhoto(photo=FileInput.from_id(os.environ["FILE_ID"]))
    result = bot.chats_set_photo(os.environ["CHAT_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models, FileInput };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let file_id = std::env::var("FILE_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::GroupSetChatPhoto::new(FileInput::from_id(file_id)?);
    let query = [];
    let result = bot.chats_set_photo(&chat_id, &query, Some(&body), Some(&request_key)).await?;
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
print(json.dumps({'photo': {'file_id': os.environ['FILE_ID']}}))
PY
)

curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/photo" \
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

## sender_chats.unban <a href="#operation-sender-chats-unban" id="operation-sender-chats-unban"></a>

`DELETE /bot-api/v1/chats/{chat_id}/sender-chat-bans/{sender_chat_id}`

Lift the ban on one channel sender in a Super Group. Your bot must be an administrator with `can_restrict_members`. Pass the channel's chat ID as `sender_chat_id`.

Only the ban on this exact channel is removed. Other bans that apply to the channel's owner stay in effect, so the owner may still be unable to post through another banned channel. Unbanning a channel that is not banned returns `true` without effects. Send an empty JSON object as the body.

### Parameters <a href="#parameters-18" id="parameters-18"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `sender_chat_id` | path | string | Yes | Use the canonical decimal ID of the channel sender to ban or unban in this group. This is a chat identity, not a user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-18" id="request-18"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-18" id="response-18"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-18" id="errors-18"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_restrict_members`. HTTP 403 `send_as_not_allowed` means the ID does not identify a channel sender. HTTP 400 `invalid_request` means `sender_chat_id` is malformed or the body is not empty. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. See [`sender_chats.ban`](groups.md#operation-sender-chats-ban).

### Example <a href="#example-18" id="example-18"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `SENDER_CHAT_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const senderChatId = process.env.SENDER_CHAT_ID;
if (!arxBotToken || !chatId || !requestKey || !senderChatId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, SENDER_CHAT_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.senderChatsUnban(chatId, senderChatId, { key: requestKey });
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
    result = bot.sender_chats_unban(os.environ["CHAT_ID"], os.environ["SENDER_CHAT_ID"], key=os.environ["REQUEST_KEY"])
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
    let request_key = std::env::var("REQUEST_KEY")?;
    let sender_chat_id = std::env::var("SENDER_CHAT_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.sender_chats_unban(&chat_id, &sender_chat_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X DELETE \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/sender-chat-bans/${SENDER_CHAT_ID}" \
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

## sender_chats.ban <a href="#operation-sender-chats-ban" id="operation-sender-chats-ban"></a>

`PUT /bot-api/v1/chats/{chat_id}/sender-chat-bans/{sender_chat_id}`

Prevent a channel from posting in a Super Group as a sender identity. Your bot must be an administrator with `can_restrict_members`. Pass the channel's chat ID as `sender_chat_id`.

While the ban applies, the channel's owner also cannot post in the group through other channels. The person can still post under their own account unless you ban them with [`members.ban`](groups.md#operation-members-ban). Banning a channel that is already banned returns `true` without new effects. Send an empty JSON object as the body.

### Parameters <a href="#parameters-19" id="parameters-19"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `sender_chat_id` | path | string | Yes | Use the canonical decimal ID of the channel sender to ban or unban in this group. This is a chat identity, not a user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-19" id="request-19"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-19" id="response-19"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-19" id="errors-19"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_restrict_members`. HTTP 403 `send_as_not_allowed` means the ID does not identify a channel sender that can be banned in this group. HTTP 400 `invalid_request` means `sender_chat_id` is malformed or the body is not empty. HTTP 404 `chat_not_found` means the group does not exist or your bot is not a member. Use [`sender_chats.unban`](groups.md#operation-sender-chats-unban) to lift the ban.

### Example <a href="#example-19" id="example-19"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `SENDER_CHAT_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const senderChatId = process.env.SENDER_CHAT_ID;
if (!arxBotToken || !chatId || !requestKey || !senderChatId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, SENDER_CHAT_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.senderChatsBan(chatId, senderChatId, { key: requestKey });
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
    result = bot.sender_chats_ban(os.environ["CHAT_ID"], os.environ["SENDER_CHAT_ID"], key=os.environ["REQUEST_KEY"])
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
    let request_key = std::env::var("REQUEST_KEY")?;
    let sender_chat_id = std::env::var("SENDER_CHAT_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.sender_chats_ban(&chat_id, &sender_chat_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/sender-chat-bans/${SENDER_CHAT_ID}" \
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

## join_request_queries.answer <a href="#operation-join-request-queries-answer" id="operation-join-request-queries-answer"></a>

`POST /bot-api/v1/join_request_queries/{query_id}/answer`

Decide a join request that your bot received as its guard. The `query_id` comes from a `chat_join_request` update that carries `query_id` and `expires_at`. Your bot acts as guard when the owner turns on the join request guard with `/setjoinrequests` in NexoCreator, the bot is an administrator with `can_invite_users`, and a group administrator selects it as the group's join request guard. See [Join requests](../groups/invites-and-requests.md#guard-join-requests).

Set `result` to `approve` to add the person, `decline` to refuse the request, or `queue` to leave it pending for the group's administrators. Answer before the update's `expires_at`. If you opened the guard Mini App with [`join_request_queries.open_mini_app`](groups.md#operation-join-request-queries-open-mini-app), the deadline is the one fixed by that call. Each query accepts one answer. A retry with the same `Idempotency-Key` and body returns `true` again.

### Parameters <a href="#parameters-20" id="parameters-20"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `query_id` | path | string | Yes | Use the opaque query ID received in the update delivered to your bot. The value must match `^[A-Za-z0-9_-]{21}[AQgw]$`. The minimum length is 22 characters. The maximum length is 22 characters. |

### Request <a href="#request-20" id="request-20"></a>

Content type: `application/json`. You must include the body.

[AnswerJoinRequestQuery](types/answerjoinrequestquery.md)

The decision for the assigned join request: approve, decline or keep the request pending. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `result` | `"approve"` or `"decline"` or `"queue"` | Yes | Choose approve to admit, decline to reject, or queue to leave this join request pending. |

### Response <a href="#response-20" id="response-20"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [GuardQueryAcknowledged](types/guardqueryacknowledged.md) in `result` |

### Errors <a href="#errors-20" id="errors-20"></a>

HTTP 410 `query_expired` means the deadline passed, the request was decided elsewhere, or your bot is no longer the guard; do not retry. HTTP 409 `query_already_answered` means the query already has an answer. HTTP 403 `capability_not_available` means the join request guard is off for your bot. See [Updates](../updates.md) for guard delivery.

### Example <a href="#example-20" id="example-20"></a>

Set `ARX_BOT_TOKEN`, `QUERY_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const queryId = process.env.QUERY_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !queryId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, QUERY_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.AnswerJoinRequestQuery = {
    "result": "queue"
  };
  const result = await bot.joinRequestQueriesAnswer(queryId, { body, key: requestKey });
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
    body = models.AnswerJoinRequestQuery(result='queue')
    result = bot.join_request_queries_answer(os.environ["QUERY_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let query_id = std::env::var("QUERY_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::AnswerJoinRequestQuery::new(models::AnswerJoinRequestQueryResult::Queue);
    let query = [];
    let result = bot.join_request_queries_answer(&query_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/join_request_queries/${QUERY_ID}/answer" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"result": "queue"}'
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

## join_request_queries.open_mini_app <a href="#operation-join-request-queries-open-mini-app" id="operation-join-request-queries-open-mini-app"></a>

`POST /bot-api/v1/join_request_queries/{query_id}/open_mini_app`

Show your bot's Mini App to the person behind a guarded join request, so they can complete a check before you decide. Use the `query_id` from the `chat_join_request` update.

`url` must be an HTTPS URL of up to 2048 characters on an origin registered for your bot. The first accepted call gives the query a new deadline of 15 minutes, or the request's own expiry if that comes sooner. Retrying with the same URL returns `true` and never extends that deadline. Opening the Mini App does not approve anyone: call [`join_request_queries.answer`](groups.md#operation-join-request-queries-answer) before the deadline, or the request stays pending for administrators.

### Parameters <a href="#parameters-21" id="parameters-21"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `query_id` | path | string | Yes | Use the opaque query ID received in the update delivered to your bot. The value must match `^[A-Za-z0-9_-]{21}[AQgw]$`. The minimum length is 22 characters. The maximum length is 22 characters. |

### Request <a href="#request-21" id="request-21"></a>

Content type: `application/json`. You must include the body.

[OpenJoinRequestMiniApp](types/openjoinrequestminiapp.md)

The registered HTTPS Mini App URL for the pending join request. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `url` | string | Yes | The HTTPS URL used for this result. The format is `uri`. The value must match `^https://`. The maximum length is 2,048 characters. |

### Response <a href="#response-21" id="response-21"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [GuardQueryAcknowledged](types/guardqueryacknowledged.md) in `result` |

### Errors <a href="#errors-21" id="errors-21"></a>

HTTP 400 `mini_app_origin_not_registered` means the URL is not HTTPS or its origin is not registered for your bot. HTTP 409 `idempotency_key_reused` means the Mini App is already open with a different URL. HTTP 410 `query_expired` means the deadline passed or the query was answered; do not retry. See [NexoCreator](../nexocreator.md) to register Mini App origins.

### Example <a href="#example-21" id="example-21"></a>

Set `ARX_BOT_TOKEN`, `QUERY_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const queryId = process.env.QUERY_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !queryId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, QUERY_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.OpenJoinRequestMiniApp = {
    "url": "https://guard.example/verify"
  };
  const result = await bot.joinRequestQueriesOpenMiniApp(queryId, { body, key: requestKey });
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
    body = models.OpenJoinRequestMiniApp(url='https://guard.example/verify')
    result = bot.join_request_queries_open_mini_app(os.environ["QUERY_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let query_id = std::env::var("QUERY_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::OpenJoinRequestMiniApp::new("https://guard.example/verify".to_owned());
    let query = [];
    let result = bot.join_request_queries_open_mini_app(&query_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/join_request_queries/${QUERY_ID}/open_mini_app" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"url": "https://guard.example/verify"}'
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

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
