# Topics

Create, edit, close, and delete forum topics in groups and private chats.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`topics.list`](#operation-topics-list) | `GET /chats/{chat_id}/topics` |
| [`topics.create`](#operation-topics-create) | `POST /chats/{chat_id}/topics` |
| [`topics.delete`](#operation-topics-delete) | `DELETE /chats/{chat_id}/topics/{topic_id}` |
| [`topics.get`](#operation-topics-get) | `GET /chats/{chat_id}/topics/{topic_id}` |
| [`topics.edit`](#operation-topics-edit) | `PATCH /chats/{chat_id}/topics/{topic_id}` |
| [`topics.close`](#operation-topics-close) | `POST /chats/{chat_id}/topics/{topic_id}/close` |
| [`topics.reopen`](#operation-topics-reopen) | `POST /chats/{chat_id}/topics/{topic_id}/reopen` |
| [`topics.unpin_all`](#operation-topics-unpin-all) | `POST /chats/{chat_id}/topics/{topic_id}/unpin_all` |
| [`topics.list_icons`](#operation-topics-list-icons) | `GET /topic_icons` |

## topics.list <a href="#operation-topics-list" id="operation-topics-list"></a>

`GET /bot-api/v1/chats/{chat_id}/topics`

List the active topics in a private chat between a user and your bot. Topics come back in ascending `topic_id` order, up to `limit` per page (1 through 100, default 100).

When `has_more` is true, pass `next_after_topic_id` as `after_topic_id` to read the next page. A full page can be followed by an empty final page. This operation reads private-chat topics only. Topics already created in a chat stay readable even when your bot can no longer create or change topics.

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `after_topic_id` | query | string | No | Return topics after this canonical creation message ID. Omit it for the first page. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-4]\|18446744073709551615)$`. |
| `limit` | query | integer | No | Set the maximum number of results to return. A response may contain fewer results. The minimum is 1. The maximum is 100. The default is `100`. |

### Request <a href="#request" id="request"></a>

Do not send a request body.

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [PrivateTopicPage](types/privatetopicpage.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

HTTP 403 `capability_not_available` means topic mode is unavailable or turned off for your bot, or the chat is a Super Group; group forum topics are not listed here. HTTP 400 `invalid_request` means `limit` or `after_topic_id` is out of range. HTTP 404 `chat_not_found` means the private chat is missing or belongs to another bot. See [topics.get](topics.md#operation-topics-get) to read one topic.

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
  const result = await bot.topicsList(chatId);
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
    result = bot.topics_list(os.environ["CHAT_ID"])
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
    let result = bot.topics_list(&chat_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/topics" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "topics": [
      {
        "topic_id": "1201",
        "version": "3",
        "name": "Billing questions",
        "icon_color": 7322096,
        "icon_key": "chat",
        "is_name_implicit": false,
        "last_message_id": "1215"
      }
    ],
    "has_more": false
  }
}
```

## topics.create <a href="#operation-topics-create" id="operation-topics-create"></a>

`POST /bot-api/v1/chats/{chat_id}/topics`

Create a named topic. The chat ID selects the form: a group forum topic in a Super Group, or a private topic in a chat between a user and your bot. Both forms require a `name` of 1 through 128 characters and accept an optional `icon_color` and `icon_key` from [topics.list_icons](topics.md#operation-topics-list-icons).

In a group forum, your bot needs the `can_manage_topics` administrator right, and forum mode must be on. The result is a forum topic with its `capabilities`. In a private chat, topic mode must be available and enabled for your bot (`has_topics_enabled` in `me.get`). The service message that creates a private topic supplies its permanent `topic_id`.

Retry a lost response with the same idempotency key and body to receive the accepted topic instead of a second one.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-1" id="request-1"></a>

Content type: `application/json`. You must include the body.

[TopicsCreate](types/topicscreate.md)

Create a named private or group forum topic.

Supply at least one of the following forms. Required fields apply within each form.

- Create a private topic with a required name and optional icon: [PrivateTopicCreate](types/privatetopiccreate.md).

- Create a named topic in a group with forums enabled: [GroupForumCreate](types/groupforumcreate.md).

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [TopicMutationResult](types/topicmutationresult.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 403 `not_enough_rights` means your bot lacks `can_manage_topics` in the group. HTTP 409 `forum_disabled` means the group is not a forum. HTTP 409 `topics_limit_reached` means the group has too many active topics; delete one first. HTTP 403 `capability_not_available` means private topic mode is unavailable or off, or forum topics are unavailable for this group. In a private chat, HTTP 400 `invalid_parameter` with reason `topic_limit_reached` means the chat already has 10,000 active topics. See [Errors](../errors.md).

### Example <a href="#example-1" id="example-1"></a>

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
  const result = await bot.topicsCreate(chatId, { key: requestKey });
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
    result = bot.topics_create(os.environ["CHAT_ID"], key=os.environ["REQUEST_KEY"])
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
    let result = bot.topics_create(&chat_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/topics" \
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
    "topic_id": "1201",
    "version": "3",
    "name": "Billing questions",
    "icon_color": 7322096,
    "icon_key": "chat",
    "is_name_implicit": false,
    "last_message_id": "1215"
  }
}
```

## topics.delete <a href="#operation-topics-delete" id="operation-topics-delete"></a>

`DELETE /bot-api/v1/chats/{chat_id}/topics/{topic_id}`

Delete a topic and its messages. The topic ID stays reserved and is never reused.

In a private chat, send the topic's current `expected_version`. The result has `lifecycle_state` set to `deleting` while its messages are removed in the background.

In a group forum, your bot needs the `can_delete_messages` administrator right, and you must send an `Idempotency-Key` header. General (topic `1`) cannot be deleted. `expected_version` is optional. Deletion is accepted first and finishes later. A first response usually has `lifecycle_state` set to `deleting` and `purge_complete` set to false. To check progress, repeat the request with the same key and body. When every message has been removed, the response returns `deleted` and `purge_complete` set to true. Deletion continues after acceptance even if your bot stops asking.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `topic_id` | path | string | Yes | The topic ID within this chat, encoded as a decimal string. Forum topic 1 is General. In a private chat the permanent ID is the creation message ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-2" id="request-2"></a>

Content type: `application/json`. You must include the body.

[TopicsDelete](types/topicsdelete.md)

Delete a private topic or a group forum topic, selected by the chat kind.

Supply at least one of the following forms. Required fields apply within each form.

- Retire the topic using its current metadata version: [PrivateTopicDelete](types/privatetopicdelete.md).

- Delete a group forum topic with DeleteMessages and a recoverable Idempotency-Key: [GroupForumDelete](types/groupforumdelete.md).

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [TopicDeletionResult](types/topicdeletionresult.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 400 `invalid_request` means the group request has no `Idempotency-Key` or targets General. HTTP 403 `not_enough_rights` means your bot lacks `can_delete_messages`. HTTP 409 `revision_conflict` means `expected_version` is stale. HTTP 404 `topic_not_found` means the group topic does not exist or is already deleted. HTTP 409 `forum_disabled` means the group is not a forum. In a private chat, HTTP 400 `invalid_parameter` with reason `topic_not_found` means the topic does not exist or was deleted. HTTP 504 `timeout` means you should repeat with the same key and body. See [Making requests](../making-requests.md) for idempotency keys.

### Example <a href="#example-2" id="example-2"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `TOPIC_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const topicId = process.env.TOPIC_ID;
if (!arxBotToken || !chatId || !requestKey || !topicId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, TOPIC_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.topicsDelete(chatId, topicId, { key: requestKey });
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
    result = bot.topics_delete(os.environ["CHAT_ID"], os.environ["TOPIC_ID"], key=os.environ["REQUEST_KEY"])
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
    let topic_id = std::env::var("TOPIC_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.topics_delete(&chat_id, &topic_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X DELETE \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/topics/${TOPIC_ID}" \
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
    "chat_id": "1099511627776",
    "lifecycle_state": "deleting",
    "purge_complete": false,
    "topic_id": "2",
    "version": "4"
  }
}
```

## topics.get <a href="#operation-topics-get" id="operation-topics-get"></a>

`GET /bot-api/v1/chats/{chat_id}/topics/{topic_id}`

Read one active topic in a private chat between a user and your bot. A private topic's `topic_id` is the ID of the service message that created it, so you can take it from a `topic_created` message or from `topics.list`.

The result carries the current `name`, `icon_color`, optional `icon_key`, `last_message_id` and `version`. Pass that `version` as `expected_version` when you edit or delete the topic. This operation reads private-chat topics only.

### Parameters <a href="#parameters-3" id="parameters-3"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `topic_id` | path | string | Yes | The topic ID within this chat, encoded as a decimal string. Forum topic 1 is General. In a private chat the permanent ID is the creation message ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-3" id="request-3"></a>

Do not send a request body.

### Response <a href="#response-3" id="response-3"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [PrivateTopic](types/privatetopic.md) in `result` |

### Errors <a href="#errors-3" id="errors-3"></a>

HTTP 400 `invalid_parameter` with reason `topic_not_found` means the topic does not exist in this chat or was deleted. HTTP 403 `capability_not_available` means topic mode is unavailable or turned off for your bot, or the chat is a Super Group. HTTP 404 `chat_not_found` means the private chat is missing or belongs to another bot. See [topics.list](topics.md#operation-topics-list) to find active topic IDs.

### Example <a href="#example-3" id="example-3"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `TOPIC_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const topicId = process.env.TOPIC_ID;
if (!arxBotToken || !chatId || !topicId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, TOPIC_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.topicsGet(chatId, topicId);
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
    result = bot.topics_get(os.environ["CHAT_ID"], os.environ["TOPIC_ID"])
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
    let topic_id = std::env::var("TOPIC_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.topics_get(&chat_id, &topic_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/topics/${TOPIC_ID}" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "topic_id": "1201",
    "version": "3",
    "name": "Billing questions",
    "icon_color": 7322096,
    "icon_key": "chat",
    "is_name_implicit": false,
    "last_message_id": "1215"
  }
}
```

## topics.edit <a href="#operation-topics-edit" id="operation-topics-edit"></a>

`PATCH /bot-api/v1/chats/{chat_id}/topics/{topic_id}`

Rename a topic or change its icon. Omit `name`, or send an empty string, to keep the current name. Omit `icon_key` to keep the icon, or send an empty string to remove it.

For a private topic, `expected_version` is required. Your bot cannot change a topic's `icon_color` after creation. For a group forum topic, `expected_version` is optional; when you send it, a stale value refuses the change. Your bot needs `can_manage_topics`, or it must be the bot that created the topic. Editing General (topic `1`) always requires `can_manage_topics`.

A real change appends a `topic_edited` service message and advances `version`. A request that changes nothing returns the topic with its version unchanged. Retry a lost response with the same idempotency key and body.

### Parameters <a href="#parameters-4" id="parameters-4"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `topic_id` | path | string | Yes | The topic ID within this chat, encoded as a decimal string. Forum topic 1 is General. In a private chat the permanent ID is the creation message ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-4" id="request-4"></a>

Content type: `application/json`. You must include the body.

[TopicsEdit](types/topicsedit.md)

Change a private or group forum topic. Private topics require expected_version; group forums permit its omission.

Supply at least one of the following forms. Required fields apply within each form.

- Change the topic name or icon using its current metadata version: [PrivateTopicEdit](types/privatetopicedit.md).

- Change a group forum topic's name or icon: [GroupForumEdit](types/groupforumedit.md).

### Response <a href="#response-4" id="response-4"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [TopicMutationResult](types/topicmutationresult.md) in `result` |

### Errors <a href="#errors-4" id="errors-4"></a>

HTTP 409 `revision_conflict` means `expected_version` is stale; read the topic and try again with its current version. HTTP 403 `not_enough_rights` means your bot cannot manage this group topic. HTTP 404 `topic_not_found` means the group topic does not exist or was deleted. In a private chat, HTTP 400 `invalid_parameter` with reason `topic_not_found` means the topic does not exist or was deleted. HTTP 409 `forum_disabled` means the group is not a forum. See [topics.get](topics.md#operation-topics-get) to read a private topic's current version.

### Example <a href="#example-4" id="example-4"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `TOPIC_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const topicId = process.env.TOPIC_ID;
if (!arxBotToken || !chatId || !requestKey || !topicId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, TOPIC_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.topicsEdit(chatId, topicId, { key: requestKey });
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
    result = bot.topics_edit(os.environ["CHAT_ID"], os.environ["TOPIC_ID"], key=os.environ["REQUEST_KEY"])
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
    let topic_id = std::env::var("TOPIC_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.topics_edit(&chat_id, &topic_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PATCH \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/topics/${TOPIC_ID}" \
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
    "topic_id": "1201",
    "version": "4",
    "name": "Billing and invoices",
    "icon_color": 7322096,
    "icon_key": "chat",
    "is_name_implicit": false,
    "last_message_id": "1215"
  }
}
```

## topics.close <a href="#operation-topics-close" id="operation-topics-close"></a>

`POST /bot-api/v1/chats/{chat_id}/topics/{topic_id}/close`

Close a group forum topic in a Super Group. This operation applies to group forums only; private-chat topics cannot be closed.

Your bot needs the `can_manage_topics` administrator right, or it must be the bot that created the topic. Closing General (topic `1`) always requires `can_manage_topics`. Send `expected_version` to refuse the change when the topic was modified after you read it. The result is the updated forum topic with `is_closed` set and its current `capabilities`.

Retry a lost response with the same idempotency key and body to receive the accepted result.

### Parameters <a href="#parameters-5" id="parameters-5"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `topic_id` | path | string | Yes | The topic ID within this chat, encoded as a decimal string. Forum topic 1 is General. In a private chat the permanent ID is the creation message ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-5" id="request-5"></a>

Content type: `application/json`. You must include the body.

[GroupForumClose](types/groupforumclose.md)

Close an existing group forum topic. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `expected_version` | string | No | Optional current metadata version. A stale value refuses the change; omission freezes the current version during acceptance. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-3]\|18446744073709551614)$`. |

### Response <a href="#response-5" id="response-5"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [ForumTopic](types/forumtopic.md) in `result` |

### Errors <a href="#errors-5" id="errors-5"></a>

HTTP 403 `not_enough_rights` means your bot cannot manage this topic. HTTP 404 `topic_not_found` means the topic does not exist in this group or was deleted. HTTP 409 `revision_conflict` means `expected_version` is stale. HTTP 409 `forum_disabled` means the group is not a forum. In a private chat, the request returns HTTP 404 `unknown_operation`. Use [topics.reopen](topics.md#operation-topics-reopen) to open the topic again.

### Example <a href="#example-5" id="example-5"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `TOPIC_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const topicId = process.env.TOPIC_ID;
if (!arxBotToken || !chatId || !requestKey || !topicId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, TOPIC_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.topicsClose(chatId, topicId, { key: requestKey });
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
    result = bot.topics_close(os.environ["CHAT_ID"], os.environ["TOPIC_ID"], key=os.environ["REQUEST_KEY"])
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
    let topic_id = std::env::var("TOPIC_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.topics_close(&chat_id, &topic_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/topics/${TOPIC_ID}/close" \
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
    "topic_id": "1201",
    "version": "4",
    "name": "Billing questions",
    "icon_color": 7322096,
    "icon_key": "chat",
    "is_name_implicit": false,
    "last_message_id": "1215",
    "is_closed": true,
    "is_hidden": false,
    "capabilities": {
      "close": false,
      "delete": true,
      "edit": true,
      "hide": false,
      "reopen": true,
      "unhide": false,
      "unpin_all": true
    }
  }
}
```

## topics.reopen <a href="#operation-topics-reopen" id="operation-topics-reopen"></a>

`POST /bot-api/v1/chats/{chat_id}/topics/{topic_id}/reopen`

Reopen a closed group forum topic in a Super Group. Reopening also makes a hidden topic visible again, so the result has both `is_closed` and `is_hidden` set to false. This operation applies to group forums only.

Your bot needs the `can_manage_topics` administrator right, or it must be the bot that created the topic. Reopening General (topic `1`) always requires `can_manage_topics`. Send `expected_version` to refuse the change when the topic was modified after you read it.

Retry a lost response with the same idempotency key and body to receive the accepted result.

### Parameters <a href="#parameters-6" id="parameters-6"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `topic_id` | path | string | Yes | The topic ID within this chat, encoded as a decimal string. Forum topic 1 is General. In a private chat the permanent ID is the creation message ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-6" id="request-6"></a>

Content type: `application/json`. You must include the body.

[GroupForumReopen](types/groupforumreopen.md)

Reopen an existing group forum topic. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `expected_version` | string | No | Optional current metadata version. A stale value refuses the change; omission freezes the current version during acceptance. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-3]\|18446744073709551614)$`. |

### Response <a href="#response-6" id="response-6"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [ForumTopic](types/forumtopic.md) in `result` |

### Errors <a href="#errors-6" id="errors-6"></a>

HTTP 403 `not_enough_rights` means your bot cannot manage this topic. HTTP 404 `topic_not_found` means the topic does not exist in this group or was deleted. HTTP 409 `revision_conflict` means `expected_version` is stale. HTTP 409 `forum_disabled` means the group is not a forum. In a private chat, the request returns HTTP 404 `unknown_operation`. See [topics.close](topics.md#operation-topics-close) for the matching close rules.

### Example <a href="#example-6" id="example-6"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `TOPIC_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const topicId = process.env.TOPIC_ID;
if (!arxBotToken || !chatId || !requestKey || !topicId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, TOPIC_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.topicsReopen(chatId, topicId, { key: requestKey });
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
    result = bot.topics_reopen(os.environ["CHAT_ID"], os.environ["TOPIC_ID"], key=os.environ["REQUEST_KEY"])
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
    let topic_id = std::env::var("TOPIC_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.topics_reopen(&chat_id, &topic_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/topics/${TOPIC_ID}/reopen" \
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
    "topic_id": "1201",
    "version": "5",
    "name": "Billing questions",
    "icon_color": 7322096,
    "icon_key": "chat",
    "is_name_implicit": false,
    "last_message_id": "1215",
    "is_closed": false,
    "is_hidden": false,
    "capabilities": {
      "close": true,
      "delete": true,
      "edit": true,
      "hide": false,
      "reopen": false,
      "unhide": false,
      "unpin_all": true
    }
  }
}
```

## topics.unpin_all <a href="#operation-topics-unpin-all" id="operation-topics-unpin-all"></a>

`POST /bot-api/v1/chats/{chat_id}/topics/{topic_id}/unpin_all`

Remove every pinned message from one topic in a private chat between a user and your bot. Pins in other topics of the chat stay in place.

This operation applies to private-chat topics only, and topic mode must be available and enabled for your bot. The result is `true` when the request is accepted. Retry a lost response with the same idempotency key and an empty body.

### Parameters <a href="#parameters-7" id="parameters-7"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `topic_id` | path | string | Yes | The topic ID within this chat, encoded as a decimal string. Forum topic 1 is General. In a private chat the permanent ID is the creation message ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-7" id="request-7"></a>

Content type: `application/json`. You must include the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-7" id="response-7"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-7" id="errors-7"></a>

HTTP 403 `capability_not_available` means topic mode is unavailable or turned off for your bot, or the chat is a Super Group. HTTP 400 `invalid_request` means the request body is not empty. HTTP 404 `chat_not_found` means the private chat is missing or belongs to another bot. HTTP 400 `invalid_parameter` with reason `topic_not_found` means the topic does not exist or was deleted. See [messages.unpin_all](messages.md#operation-messages-unpin-all) to remove the pins of a whole chat.

### Example <a href="#example-7" id="example-7"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `REQUEST_KEY`, `TOPIC_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const requestKey = process.env.REQUEST_KEY;
const topicId = process.env.TOPIC_ID;
if (!arxBotToken || !chatId || !requestKey || !topicId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, REQUEST_KEY, TOPIC_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.topicsUnpinAll(chatId, topicId, { key: requestKey });
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
    result = bot.topics_unpin_all(os.environ["CHAT_ID"], os.environ["TOPIC_ID"], key=os.environ["REQUEST_KEY"])
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
    let topic_id = std::env::var("TOPIC_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.topics_unpin_all(&chat_id, &topic_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/topics/${TOPIC_ID}/unpin_all" \
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

## topics.list_icons <a href="#operation-topics-list-icons" id="operation-topics-list-icons"></a>

`GET /bot-api/v1/topic_icons`

Read the catalogue of topic icons. Each entry pairs an icon `key` with an SVG vector, and `colors` lists the six RGB colors a topic icon can use. Use these keys for `icon_key` and these values for `icon_color` when you create or edit a topic.

Render each SVG with the topic's color. The catalogue is versioned; cache it by `version` and read it again when the version changes. The request takes no query parameters or body.

### Parameters <a href="#parameters-8" id="parameters-8"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-8" id="request-8"></a>

Do not send a request body.

### Response <a href="#response-8" id="response-8"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [PrivateTopicIcons](types/privatetopicicons.md) in `result` |

### Errors <a href="#errors-8" id="errors-8"></a>

HTTP 400 `invalid_request` means the request included query parameters or a body. HTTP 401 `unauthorized` means your bot token is missing, invalid or revoked. See [topics.create](topics.md#operation-topics-create) to use an icon key.

### Example <a href="#example-8" id="example-8"></a>

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
  const result = await bot.topicsListIcons();
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
    result = bot.topics_list_icons()
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
    let result = bot.topics_list_icons(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/topic_icons" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "version": "1",
    "colors": [
      7322096,
      16766590,
      13338331,
      9367192,
      16749490,
      16478047
    ],
    "icons": [
      {
        "key": "chat",
        "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 24 24\"><path d=\"M4 4h16v12H7l-3 3z\"/></svg>"
      }
    ]
  }
}
```

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
