# Drafts and chat actions

Show progress while your bot prepares a reply, then send the final message.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`chats.send_action`](#operation-chats-send-action) | `POST /chats/{chat_id}/actions` |
| [`drafts.delete`](#operation-drafts-delete) | `DELETE /chats/{chat_id}/drafts/{draft_id}` |
| [`drafts.set`](#operation-drafts-set) | `PUT /chats/{chat_id}/drafts/{draft_id}` |

## chats.send_action <a href="#operation-chats-send-action" id="operation-chats-send-action"></a>

`POST /bot-api/v1/chats/{chat_id}/actions`

Show an activity indicator while your bot prepares a reply. Send a message when the work is complete.

The indicator lasts up to five seconds and disappears when your bot sends a message. Repeated calls within one second are combined. Choose an action from the request's accepted values.

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request" id="request"></a>

Content type: `application/json`. You must include the body.

[SendAction](types/sendaction.md)

The temporary activity your bot displays in a chat. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `action` | `"typing"` or `"upload_photo"` or `"record_video"` or `"upload_video"` or `"record_voice"` or `"upload_voice"` or `"upload_document"` or `"choose_sticker"` or `"find_location"` or `"record_video_note"` or `"upload_video_note"` | Yes | Choose the activity that your bot displays in the chat. |

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

HTTP 403 `bot_blocked_by_user` means you should stop sending to that user. See [Drafts](../drafts.md) when you also want to show the reply as it develops.

### Example <a href="#example" id="example"></a>

Use a new idempotency key for each new action. Reuse the example key only when retrying this exact request.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.chatsSendAction("4398046511200", {
    "body": {
      "action": "typing"
    },
    "key": "example-chats-send_action-1"
  });
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
from arx_nexo import Client, Config
from arx_nexo import models

with Client(Config(base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.SendAction.model_validate({'action': 'typing'})
    result = bot.chats_send_action('4398046511200', body=body, key='example-chats-send_action-1')
```

{% endtab %}

{% tab title="Rust" %}

Add `serde_json = "1"` to your application's `[dependencies]` in `Cargo.toml`.

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let config = Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?;
    let bot = Client::new(config)?;
    let body: arx_nexo::models::SendAction = serde_json::from_value(serde_json::json!({
      "action": "typing"
    }))?;
    let result = bot.chats_send_action("4398046511200", &[], Some(&body), Some("example-chats-send_action-1")).await?;
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/4398046511200/actions" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: example-chats-send_action-1" \
  -H "Content-Type: application/json" \
  --data '{"action": "typing"}'
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

## drafts.delete <a href="#operation-drafts-delete" id="operation-drafts-delete"></a>

`DELETE /bot-api/v1/chats/{chat_id}/drafts/{draft_id}`

Remove a visible draft before it expires. Pass the same chat ID and draft ID that you used with `drafts.set`.

Deleting a draft does not send a final reply. Use `messages.send` when you have a reply to deliver. See [Drafts](../drafts.md) for the generation lifecycle.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `draft_id` | path | string | Yes | Choose a draft identifier. Use the same ID to update the draft. The value must match `^[A-Za-z0-9_-]{1,128}$`. |

### Request <a href="#request-1" id="request-1"></a>

Do not send a request body.

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 404 `chat_not_found` means the chat is missing or inaccessible. HTTP 403 `bot_blocked_by_user` means you should stop attempting to show drafts in that chat.

### Example <a href="#example-1" id="example-1"></a>

Use a new idempotency key for each new action. Reuse the example key only when retrying this exact request.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.draftsDelete("4398046511200", "reply-1", {
    "key": "example-drafts-delete-1"
  });
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
from arx_nexo import Client, Config

with Client(Config(base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.drafts_delete('4398046511200', 'reply-1', key='example-drafts-delete-1')
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let config = Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?;
    let bot = Client::new(config)?;
    let result = bot.drafts_delete("4398046511200", "reply-1", &[], None, Some("example-drafts-delete-1")).await?;
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X DELETE \
  "https://signal.arx.pro/bot-api/v1/chats/4398046511200/drafts/reply-1" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: example-drafts-delete-1"
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

## drafts.set <a href="#operation-drafts-set" id="operation-drafts-set"></a>

`PUT /bot-api/v1/chats/{chat_id}/drafts/{draft_id}`

Show a temporary draft while your bot generates a reply. Supply exactly one of `text` or `rich`, then refresh the draft within 30 seconds if generation continues.

Use an existing library file ID for media in a rich draft. Send the final reply with `messages.send`. A draft does not become a message by itself. The result is `true`. Repeating the same key and request returns `true` without extending the draft's lifetime.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `draft_id` | path | string | Yes | Choose a draft identifier. Use the same ID to update the draft. The value must match `^[A-Za-z0-9_-]{1,128}$`. |

### Request <a href="#request-2" id="request-2"></a>

Content type: `application/json`. You must include the body.

[SetDraft](types/setdraft.md)

The text or rich content and stop controls for a temporary draft. Use drafts in private chats to show a response while you generate it. A draft lasts for 30 s and stays out of message history. You can use a thinking block in a rich draft, and media must reference existing file IDs. You can update drafts up to 5 times per second per chat. Reusing a stopped draft ID returns HTTP 409 with code version_conflict and reason draft_retired. Sending a final message retires the draft. Deleting the final message does not prevent you from creating a new draft. Only the listed fields are allowed. Do not supply `format` and `entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `can_stop` | boolean | No | This is true when the user can stop generation of this draft. The default is `false`. |
| `entities` | Array of [InputEntity](types/inputentity.md) | No | These define formatting with offsets and lengths measured in UTF-16 code units. In requests, use these instead of format. |
| `format` | `"plain"` or `"html"` or `"markdown"` | No | Choose the markup format used to parse text. Use this instead of entities. |
| `keep_on_stop` | boolean | No | This is true when the current draft remains visible when the user stops generation. The default is `false`. |
| `rich` | [InputDraftRich](types/inputdraftrich.md) | No | This contains structured rich message content. |
| `text` | string | No | The temporary draft text shown while your bot generates a response. You can use 0 to 4,096 UTF-16 code units after markup is parsed. An empty string displays the thinking indicator. |

Supply exactly one of the following forms. Required fields apply within each form.

- Include `text`. Omit `rich`.

- Include `rich`. Omit `text`, `format`, and `entities`.

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 409 `version_conflict` with reason `draft_retired` means this generation has ended. Use a new draft ID for a new generation. HTTP 409 `idempotency_key_reused` means the same key was used with different parameters. See [Drafts](../drafts.md) for Stop handling and the final-send sequence.

### Example <a href="#example-2" id="example-2"></a>

Use a new idempotency key for each new action. Reuse the example key only when retrying this exact request.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.draftsSet("4398046511200", "reply-1", {
    "body": {
      "can_stop": true,
      "keep_on_stop": false,
      "text": "Working…"
    },
    "key": "example-drafts-set-1"
  });
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
from arx_nexo import Client, Config
from arx_nexo import models

with Client(Config(base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.SetDraft.model_validate({'can_stop': True, 'keep_on_stop': False, 'text': 'Working…'})
    result = bot.drafts_set('4398046511200', 'reply-1', body=body, key='example-drafts-set-1')
```

{% endtab %}

{% tab title="Rust" %}

Add `serde_json = "1"` to your application's `[dependencies]` in `Cargo.toml`.

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let config = Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?;
    let bot = Client::new(config)?;
    let body: arx_nexo::models::SetDraft = serde_json::from_value(serde_json::json!({
      "can_stop": true,
      "keep_on_stop": false,
      "text": "Working…"
    }))?;
    let result = bot.drafts_set("4398046511200", "reply-1", &[], Some(&body), Some("example-drafts-set-1")).await?;
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/chats/4398046511200/drafts/reply-1" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: example-drafts-set-1" \
  -H "Content-Type: application/json" \
  --data '{"can_stop": true, "keep_on_stop": false, "text": "Working…"}'
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
