# Inline and Mini Apps

Answer inline, guest, and Mini App queries, and save prepared messages and buttons.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`guest_queries.answer`](#operation-guest-queries-answer) | `POST /guest_queries/{query_id}/answer` |
| [`inline_queries.answer`](#operation-inline-queries-answer) | `POST /inline_queries/{inline_query_id}/answer` |
| [`mini_app_queries.answer`](#operation-mini-app-queries-answer) | `POST /mini_app_queries/{query_id}/answer` |
| [`prepared_buttons.save`](#operation-prepared-buttons-save) | `POST /prepared_buttons` |
| [`prepared_messages.save`](#operation-prepared-messages-save) | `POST /prepared_messages` |

## guest_queries.answer <a href="#operation-guest-queries-answer" id="operation-guest-queries-answer"></a>

`POST /bot-api/v1/guest_queries/{query_id}/answer`

Answer a guest invocation, where a user calls your bot in a chat it is not a member of. Your bot receives a `guest_message` update with a `guest_query_id` and an `answer_deadline_at`; it gets no access to the chat's history, members or later messages. Pass `guest_query_id` as `query_id` with one inline `result`.

Each query accepts one answer before its deadline. Supported content is text, rich text, location, venue and contact; attached media is not supported. The returned `inline_message_id` confirms acceptance, not delivery to other devices. The request has no idempotency key, so a second attempt after a lost response returns `query_already_answered`.

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `query_id` | path | string | Yes | Use the opaque query ID received in the update delivered to your bot. The value must match `^[0-9a-f]{32}$`. |

### Request <a href="#request" id="request"></a>

Content type: `application/json`. You must include the body.

[AnswerGuestQuery](types/answerguestquery.md)

The single inline result to answer a query issued to your bot. Unsupported content is rejected before accepting an answer. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `result` | [InlineResult](types/inlineresult.md) | Yes | The inline result to deliver for this query. |

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [SentGuestMessage](types/sentguestmessage.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

HTTP 409 `query_already_answered` means an answer was already accepted for this query. HTTP 410 `query_expired` means the deadline passed. HTTP 404 `query_not_found` means the ID is unknown or belongs to another bot. HTTP 403 `capability_not_available` means the content type is unsupported; the query is not used and you can answer again with supported content. HTTP 400 `result_invalid` means a result field is invalid. HTTP 400 `invalid_request` means the body is malformed or has fields other than `result`. See [Updates](../updates.md).

### Example <a href="#example" id="example"></a>

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
  const body: Models.AnswerGuestQuery = {
    "result": {
      "content": {
        "text": "Hello",
        "type": "text"
      },
      "id": "reply",
      "title": "Reply",
      "type": "article"
    }
  };
  const result = await bot.guestQueriesAnswer(queryId, { body, key: requestKey });
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
    body = models.AnswerGuestQuery(
        result=models.InlineResult(
            root=models.InlineResultArticle(
                content=models.InlineMessageContent(root=models.InlineText(text='Hello', type='text')),
                id='reply',
                title='Reply',
                type='article',
            ),
        ),
    )
    result = bot.guest_queries_answer(os.environ["QUERY_ID"], body=body, key=os.environ["REQUEST_KEY"])
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
    let body = models::AnswerGuestQuery::new(
        models::InlineResult::InlineResultArticle(
            Box::new(
                models::InlineResultArticle::new(
                    models::InlineMessageContent::InlineText(
                        Box::new(models::InlineText::new("Hello".to_owned())),
                    ),
                    "reply".to_owned(),
                    "Reply".to_owned(),
                ),
            ),
        ),
    );
    let query = [];
    let result = bot.guest_queries_answer(&query_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/guest_queries/${QUERY_ID}/answer" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"result": {"content": {"text": "Hello", "type": "text"}, "id": "reply", "title": "Reply", "type": "article"}}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "inline_message_id": "ad415663c059425891ef005d86d46cba"
  }
}
```

## inline_queries.answer <a href="#operation-inline-queries-answer" id="operation-inline-queries-answer"></a>

`POST /bot-api/v1/inline_queries/{inline_query_id}/answer`

Answer an `inline_query` update with up to 50 results for the user who typed the query. You can accept one answer within the query's original 30-second deadline, shown in its `expires_at`. A successful answer returns `true`.

Answers are personal and uncached: leave `cache_seconds` at 0, `is_personal` at true and `next_offset` empty. You can add a `button` that opens your Mini App above the results, even when `results` is empty. Its URL must use an HTTPS origin currently registered to your bot. The answer can be at most 128 KiB.

The `Idempotency-Key` header is required. If a response is lost, repeat the request with the same key and body before the deadline.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `inline_query_id` | path | string | Yes | Use the exact canonical base64url inline query ID created for this bot and caller. It is not a Mini App init-data query ID. The value must match `^[A-Za-z0-9_-]{21}[AQgw]$`. The minimum length is 22 characters. The maximum length is 22 characters. |

### Request <a href="#request-1" id="request-1"></a>

Content type: `application/json`. You must include the body.

[AnswerSavedInlineQuery](types/answersavedinlinequery.md)

One personal uncached answer within the saved query's original deadline. Unsupported cache and pagination options are rejected; only the Mini App header button is supported. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `button` | [SavedInlineAppButton](types/savedinlineappbutton.md) | No | Optional Mini App header button shown above the results, including when the results array is empty. |
| `cache_seconds` | `0` | No | Must be zero. Saved inline answers are not cached and retain the query's original deadline. The default is `0`. |
| `is_personal` | `true` | No | Must be true. The answer is personal to the user who issued this query. The default is `true`. |
| `next_offset` | `""` | No | Must be empty. Saved inline answers do not support pagination. The default is `""`. |
| `results` | Array of [InlineResult](types/inlineresult.md) | Yes | Up to 50 supported inline results. An empty array can still show the Mini App header button. |

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 409 `query_already_answered` means another answer was accepted. HTTP 410 `query_expired` means the deadline passed; do not retry. HTTP 404 `query_not_found` means the ID is unknown or belongs to another bot. HTTP 403 `capability_not_available` means inline answers are unavailable to your bot or an option has an unsupported value. HTTP 403 `mini_app_disabled` means the button's origin is not registered or Mini Apps are off. HTTP 413 `request_too_large` means the answer exceeds 128 KiB. HTTP 400 `result_invalid` means a result is invalid or repeats an `id`. HTTP 409 `idempotency_key_reused` means the key was used with a different body. See [Updates](../updates.md).

### Example <a href="#example-1" id="example-1"></a>

Set `ARX_BOT_TOKEN`, `INLINE_QUERY_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const inlineQueryId = process.env.INLINE_QUERY_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !inlineQueryId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, INLINE_QUERY_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.AnswerSavedInlineQuery = {
    "button": {
      "mini_app": {
        "url": "https://app.example.test/inline"
      },
      "text": "Open"
    },
    "cache_seconds": 0,
    "is_personal": true,
    "results": []
  };
  const result = await bot.inlineQueriesAnswer(inlineQueryId, { body, key: requestKey });
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
    body = models.AnswerSavedInlineQuery(
        button=models.SavedInlineAppButton(
            mini_app=models.SavedInlineAppButtonMiniApp(url='https://app.example.test/inline'),
            text='Open',
        ),
        cache_seconds=0,
        is_personal=True,
        results=[],
    )
    result = bot.inline_queries_answer(os.environ["INLINE_QUERY_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let inline_query_id = std::env::var("INLINE_QUERY_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::AnswerSavedInlineQuery {
        button: Some(
            models::SavedInlineAppButton::new(
                models::SavedInlineAppButtonMiniApp::new("https://app.example.test/inline".to_owned()),
                "Open".to_owned(),
            ),
        ),
        cache_seconds: Some(0),
        is_personal: Some(true),
        ..models::AnswerSavedInlineQuery::new(vec![])
    };
    let query = [];
    let result = bot.inline_queries_answer(&inline_query_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/inline_queries/${INLINE_QUERY_ID}/answer" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"button": {"mini_app": {"url": "https://app.example.test/inline"}, "text": "Open"}, "cache_seconds": 0, "is_personal": true, "results": []}'
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

## mini_app_queries.answer <a href="#operation-mini-app-queries-answer" id="operation-mini-app-queries-answer"></a>

`POST /bot-api/v1/mini_app_queries/{query_id}/answer`

Send one result as the user who launched your Mini App. When a launch carries a `query_id` in its init data, your Mini App passes it to your server and your bot answers with one inline `result`. The message is sent on the user's behalf into the chat the Mini App was opened from.

The response has `delivery` set to `sent`. It includes an `inline_message_id` when the result has a keyboard, so you can update that message later. Send an `Idempotency-Key` header and repeat the same key and body after a lost response to receive the confirmed result without sending again.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `query_id` | path | string | Yes | Use the opaque query ID from verified Mini App init data. The value must match `^[0-9a-f]{32}$`. |

### Request <a href="#request-2" id="request-2"></a>

Content type: `application/json`. You must include the body.

[AnswerMiniAppQuery](types/answerminiappquery.md)

One result to send in response to the Mini App query. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `result` | [InlineResult](types/inlineresult.md) | Yes | The inline result to deliver for this query. |

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [SentMiniAppMessage](types/sentminiappmessage.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 429 `mini_app_query_too_early` means you must wait `retry_after` seconds before answering. HTTP 409 `query_already_answered` means a result was already sent. HTTP 410 `query_expired` means the query can no longer be answered. HTTP 404 `query_not_found` means the ID is unknown or belongs to another bot. HTTP 400 `result_invalid` names the invalid field, and HTTP 400 `unsupported_content` means the result cannot be sent for this launch. HTTP 403 `capability_not_available` means the result is a game or sticker, shares a live location or has a callback button. HTTP 409 `idempotency_key_reused` means the key was used with a different body. See [Errors](../errors.md).

### Example <a href="#example-2" id="example-2"></a>

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
  const body: Models.AnswerMiniAppQuery = {
    "result": {
      "content": {
        "text": "Hello",
        "type": "text"
      },
      "id": "note",
      "title": "Note",
      "type": "article"
    }
  };
  const result = await bot.miniAppQueriesAnswer(queryId, { body, key: requestKey });
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
    body = models.AnswerMiniAppQuery(
        result=models.InlineResult(
            root=models.InlineResultArticle(
                content=models.InlineMessageContent(root=models.InlineText(text='Hello', type='text')),
                id='note',
                title='Note',
                type='article',
            ),
        ),
    )
    result = bot.mini_app_queries_answer(os.environ["QUERY_ID"], body=body, key=os.environ["REQUEST_KEY"])
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
    let body = models::AnswerMiniAppQuery::new(
        models::InlineResult::InlineResultArticle(
            Box::new(
                models::InlineResultArticle::new(
                    models::InlineMessageContent::InlineText(
                        Box::new(models::InlineText::new("Hello".to_owned())),
                    ),
                    "note".to_owned(),
                    "Note".to_owned(),
                ),
            ),
        ),
    );
    let query = [];
    let result = bot.mini_app_queries_answer(&query_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/mini_app_queries/${QUERY_ID}/answer" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"result": {"content": {"text": "Hello", "type": "text"}, "id": "note", "title": "Note", "type": "article"}}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "delivery": "sent"
  }
}
```

## prepared_buttons.save <a href="#operation-prepared-buttons-save" id="operation-prepared-buttons-save"></a>

`POST /bot-api/v1/prepared_buttons`

Save a keyboard button that your Mini App can offer one user. Pass the user's `user_id` and a `button` of type `request_users`, which opens the native account picker with the button's filters, or `request_managed_bot`, which opens native managed-bot creation with editable name suggestions. The user must be a human user your bot already knows.

The response returns an `id` and an `expires_at` 24 hours after saving. Pass the `id` to the Mini App method `requestChat`; only that user and your bot can use it. A managed-bot button also requires managed-bot creation to be available and turned on for your bot (`can_manage_bots`); the user confirms creation in the app. At most 100 saved buttons stay active for one bot and user. Send an `Idempotency-Key` header to retry safely.

### Parameters <a href="#parameters-3" id="parameters-3"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-3" id="request-3"></a>

Content type: `application/json`. You must include the body.

[SavePreparedKeyboardButton](types/savepreparedkeyboardbutton.md)

A button bound to a known human account and this bot. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `button` | [PreparedUsersButton](types/preparedusersbutton.md) or [PreparedManagedButton](types/preparedmanagedbutton.md) | Yes | This defines an interactive button embedded in rich text. |
| `user_id` | string | Yes | This is the user ID, encoded as a decimal string. A user ID differs from a private chat ID. The value must match `^[1-9][0-9]*$`. |

### Response <a href="#response-3" id="response-3"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [PreparedKeyboardButton](types/preparedkeyboardbutton.md) in `result` |

### Errors <a href="#errors-3" id="errors-3"></a>

HTTP 429 `rate_limited` with reason `prepared_button_quota` means 100 buttons are active; wait `retry_after` seconds. HTTP 404 `user_not_found` means the user is unknown to your bot or is a bot. HTTP 403 `capability_not_available` means Mini Apps or the button type are unavailable. HTTP 400 `invalid_request` identifies an invalid `button`. HTTP 400 `request_not_valid` means a `request_managed_bot` button was sent while your bot cannot create managed bots for this user, for example because `can_manage_bots` is off. Other HTTP 429 `rate_limited` responses mean you saved buttons for this user too fast; wait `retry_after` seconds. See [Keyboards](../keyboards.md).

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
  const body: Models.SavePreparedKeyboardButton = {
    "button": {
      "max_quantity": 2,
      "request_id": "contacts",
      "request_username": true,
      "text": "Choose contacts",
      "type": "request_users"
    },
    "user_id": "700001234"
  };
  const result = await bot.preparedButtonsSave({ body, key: requestKey });
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
    body = models.SavePreparedKeyboardButton(
        button=models.SavePreparedKeyboardButtonButton(
            root=models.PreparedUsersButton(
                max_quantity=2,
                request_id='contacts',
                request_username=True,
                text='Choose contacts',
                type='request_users',
            ),
        ),
        user_id='700001234',
    )
    result = bot.prepared_buttons_save(body=body, key=os.environ["REQUEST_KEY"])
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
    let body = models::SavePreparedKeyboardButton::new(
        models::SavePreparedKeyboardButtonButton::PreparedUsersButton(
            Box::new(
                models::PreparedUsersButton {
                    max_quantity: Some(2),
                    request_username: Some(true),
                    ..models::PreparedUsersButton::new("contacts".to_owned(), "Choose contacts".to_owned())
                },
            ),
        ),
        "700001234".to_owned(),
    );
    let query = [];
    let result = bot.prepared_buttons_save(&query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/prepared_buttons" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"button": {"max_quantity": 2, "request_id": "contacts", "request_username": true, "text": "Choose contacts", "type": "request_users"}, "user_id": "700001234"}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "expires_at": "2026-10-24T12:00:00Z",
    "id": "ad415663c059425891ef005d86d46cba"
  }
}
```

## prepared_messages.save <a href="#operation-prepared-messages-save" id="operation-prepared-messages-save"></a>

`POST /bot-api/v1/prepared_messages`

Save one inline `result` that your Mini App can offer a user to share. Pass the user's `user_id` and choose at least one destination type with `allow_user_chats`, `allow_bot_chats`, `allow_group_chats` or `allow_channel_chats`. The recipient must be a human user your bot already knows.

The response returns an `id` and an `expires_at` 24 hours after saving. Pass the `id` to the Mini App method `shareMessage`; the user picks a destination in the app and the message is sent from their account. Only that user and your bot can use the saved message. Send an `Idempotency-Key` header and repeat the same key and body to receive the same saved message after a lost response.

### Parameters <a href="#parameters-4" id="parameters-4"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-4" id="request-4"></a>

Content type: `application/json`. You must include the body.

[SavePreparedMessage](types/savepreparedmessage.md)

A reusable result for one known human recipient and the allowed destination types. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `allow_bot_chats` | boolean | No | Allow the recipient to share the prepared result to private chats with bots. The default is `false`. |
| `allow_channel_chats` | boolean | No | Allow the recipient to share the prepared result to channels where they may publish. The default is `false`. |
| `allow_group_chats` | boolean | No | Allow the recipient to share the prepared result to groups. The default is `false`. |
| `allow_user_chats` | boolean | No | Allow the recipient to share the prepared result to private chats with people. The default is `false`. |
| `result` | [InlineResult](types/inlineresult.md) | Yes | The inline result to save for later use as a prepared message. |
| `user_id` | string | Yes | This is the user ID, encoded as a decimal string. A user ID differs from a private chat ID. The value must match `^[1-9][0-9]*$`. |

### Response <a href="#response-4" id="response-4"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [PreparedMessageReceipt](types/preparedmessagereceipt.md) in `result` |

### Errors <a href="#errors-4" id="errors-4"></a>

HTTP 400 `prepared_message_invalid` means the result or destination selection is invalid; check `field`. HTTP 404 `user_not_found` means the user is unknown to your bot or is a bot. HTTP 400 `unsupported_content` means the result type cannot be saved. HTTP 403 `capability_not_available` means Mini App sharing is unavailable to your bot. HTTP 409 `idempotency_key_reused` means the key was used with another body. See [Errors](../errors.md).

### Example <a href="#example-4" id="example-4"></a>

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
  const body: Models.SavePreparedMessage = {
    "allow_bot_chats": true,
    "result": {
      "content": {
        "text": "Hello",
        "type": "text"
      },
      "id": "note",
      "title": "Note",
      "type": "article"
    },
    "user_id": "700001234"
  };
  const result = await bot.preparedMessagesSave({ body, key: requestKey });
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
    body = models.SavePreparedMessage(
        allow_bot_chats=True,
        result=models.InlineResult(
            root=models.InlineResultArticle(
                content=models.InlineMessageContent(root=models.InlineText(text='Hello', type='text')),
                id='note',
                title='Note',
                type='article',
            ),
        ),
        user_id='700001234',
    )
    result = bot.prepared_messages_save(body=body, key=os.environ["REQUEST_KEY"])
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
    let body = models::SavePreparedMessage {
        allow_bot_chats: Some(true),
        ..models::SavePreparedMessage::new(
            models::InlineResult::InlineResultArticle(
                Box::new(
                    models::InlineResultArticle::new(
                        models::InlineMessageContent::InlineText(
                            Box::new(models::InlineText::new("Hello".to_owned())),
                        ),
                        "note".to_owned(),
                        "Note".to_owned(),
                    ),
                ),
            ),
            "700001234".to_owned(),
        )
    };
    let query = [];
    let result = bot.prepared_messages_save(&query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/prepared_messages" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"allow_bot_chats": true, "result": {"content": {"text": "Hello", "type": "text"}, "id": "note", "title": "Note", "type": "article"}, "user_id": "700001234"}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "expires_at": "2026-10-24T12:00:00Z",
    "id": "ad415663c059425891ef005d86d46cba"
  }
}
```

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
