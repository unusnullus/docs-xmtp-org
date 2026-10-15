# Interactive messages

Answer button presses and work with reactions, polls, and live locations.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`callback_queries.answer`](#operation-callback-queries-answer) | `POST /callback_queries/{callback_query_id}/answer` |
| [`reactions.set`](#operation-reactions-set) | `PUT /chats/{chat_id}/messages/{message_id}/my_reaction` |
| [`messages.delete_poll_option`](#operation-messages-delete-poll-option) | `DELETE /chats/{chat_id}/messages/{message_id}/poll_options/{option_id}` |
| [`messages.stop_live_location`](#operation-messages-stop-live-location) | `POST /chats/{chat_id}/messages/{message_id}/stop_live_location` |
| [`messages.stop_poll`](#operation-messages-stop-poll) | `POST /chats/{chat_id}/messages/{message_id}/stop_poll` |
| [`message_effects.list`](#operation-message-effects-list) | `GET /message_effects` |

## callback_queries.answer <a href="#operation-callback-queries-answer" id="operation-callback-queries-answer"></a>

`POST /bot-api/v1/callback_queries/{callback_query_id}/answer`

Answer a callback button press with a short message, an alert, or a link to your bot. You should answer every query, even when you have nothing to display.

You can accept one answer within 15 minutes of the press. The app waits up to 15 seconds to display it or navigate. Use the same idempotency key and body to retry the accepted answer. To open your bot, use its current handle and a `start` parameter as described in [Deep links](../deep-links.md).

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `callback_query_id` | path | string | Yes | Use the UUID of the callback query received in an update. The format is `uuid`. |

### Request <a href="#request" id="request"></a>

Content type: `application/json`. You may omit the body.

[CallbackAnswer](types/callbackanswer.md)

The notification, alert, or bot start link used to answer an inline button press. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `cache_seconds` | integer | No | This specifies how long the client may cache this callback answer, in seconds. The minimum is 0. The maximum is 3,600. The default is `0`. |
| `show_alert` | boolean | No | This is true when the callback answer appears as an alert instead of a notification. The default is `false`. |
| `text` | string | No | The plain notification text shown after the button press, up to 200 UTF-16 code units. Set show_alert to display it as an alert instead. Markup is not parsed. |
| `url` | string | No | A start link for your own bot in the form https://www.arx.pro/bot/&lt;handle>?start=&lt;payload>. The payload must contain 1 to 64 ASCII letters, digits, underscores, or hyphens. Other destinations are rejected. Use a start link for your bot's current handle. Other bots and external URLs are not accepted. An invalid URL returns invalid_parameter with field set to url. For example, `https://www.arx.pro/bot/ACMESUPPORTBOT?start=help_42`. The value must match `^https://www\.arx\.pro/bot/[A-Za-z0-9_]+\?start=[A-Za-z0-9_-]{1,64}$`. The maximum length is 2,048 characters. |

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

For an invalid link, HTTP 400 `invalid_parameter` identifies `url`; correct it before the query expires. HTTP 409 `query_already_answered` means another answer was accepted. HTTP 410 `query_expired` means you must wait for a new button press.

### Example <a href="#example" id="example"></a>

Set `ARX_BOT_TOKEN`, `CALLBACK_QUERY_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const callbackQueryId = process.env.CALLBACK_QUERY_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !callbackQueryId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CALLBACK_QUERY_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.CallbackAnswer = {
    "text": "Saved",
    "show_alert": false,
    "cache_seconds": 0
  };
  const result = await bot.callbackQueriesAnswer(callbackQueryId, { body, key: requestKey });
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
    body = models.CallbackAnswer(text='Saved', show_alert=False, cache_seconds=0)
    result = bot.callback_queries_answer(os.environ["CALLBACK_QUERY_ID"], body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let callback_query_id = std::env::var("CALLBACK_QUERY_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::CallbackAnswer {
        cache_seconds: Some(0),
        show_alert: Some(false),
        text: Some("Saved".to_owned()),
        url: None,
    };
    let query = [];
    let result = bot.callback_queries_answer(&callback_query_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/callback_queries/${CALLBACK_QUERY_ID}/answer" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"text": "Saved", "show_alert": false, "cache_seconds": 0}'
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

## reactions.set <a href="#operation-reactions-set" id="operation-reactions-set"></a>

`PUT /bot-api/v1/chats/{chat_id}/messages/{message_id}/my_reaction`

Set your bot's emoji reaction on a message, or remove it by sending an empty `reactions` array.

Your bot can select at most one emoji. Read the supported values from `platform.get`. For an album, the reaction applies to its first visible message that has not been deleted. Reaction updates use that message's ID.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `message_id` | path | string | Yes | Use the ID of the message within this chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-1" id="request-1"></a>

Content type: `application/json`. You must include the body.

[SetReactions](types/setreactions.md)

The reaction to apply to a message. An empty list removes your bot's reaction. When you react to an album item, the first surviving visible item is the target. If it is deleted, the next item becomes the target. Repeating the current reaction leaves the message unchanged. An idempotent replay returns the original result. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `is_big` | boolean | No | Set this to true to display a larger reaction animation. |
| `reactions` | Array of [Reaction](types/reaction.md) | Yes | This contains reactions to apply or the current reaction state. An empty input array removes your bot's reaction. The maximum is 1 item. |

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 400 `reaction_not_allowed` means the selected reaction is unavailable. HTTP 404 `message_not_found` means the target is missing or inaccessible. Continue with [Updates](../updates.md) to receive reaction changes.

### Example <a href="#example-1" id="example-1"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `MESSAGE_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const messageId = process.env.MESSAGE_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !messageId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, MESSAGE_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.SetReactions = {
    "is_big": false,
    "reactions": [{
      "emoji": "👍",
      "type": "emoji"
    }]
  };
  const result = await bot.reactionsSet(chatId, messageId, { body, key: requestKey });
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
    body = models.SetReactions(is_big=False, reactions=[models.Reaction(emoji='👍', type='emoji')])
    result = bot.reactions_set(os.environ["CHAT_ID"], os.environ["MESSAGE_ID"], body=body, key=os.environ["REQUEST_KEY"])
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
    let message_id = std::env::var("MESSAGE_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::SetReactions {
        is_big: Some(false),
        reactions: vec![models::Reaction {
            emoji: "👍".to_owned(),
            r#type: models::ReactionType::Emoji,
        }],
    };
    let query = [];
    let result = bot.reactions_set(&chat_id, &message_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/${MESSAGE_ID}/my_reaction" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"is_big": false, "reactions": [{"emoji": "👍", "type": "emoji"}]}'
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

## messages.delete_poll_option <a href="#operation-messages-delete-poll-option" id="operation-messages-delete-poll-option"></a>

`DELETE /bot-api/v1/chats/{chat_id}/messages/{message_id}/poll_options/{option_id}`

Remove a user-added option from an open poll authored by your bot. Use the option's persistent UUID in the path. Omit the body or send `{}`.

The poll must be regular, non-anonymous, and allow added options. You cannot delete an original option. Other option IDs remain stable. Removing an option adjusts the affected votes and counts without creating a `poll_answer` update.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `message_id` | path | string | Yes | Use the ID of the message within this chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `option_id` | path | string | Yes | Use the UUID of the poll option to remove, rather than its position. The format is `uuid`. |

### Request <a href="#request-2" id="request-2"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Poll](types/poll.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 403 `access_restricted` means another author owns the poll. HTTP 409 `poll_closed` means it is already closed. HTTP 400 `poll_option_invalid` with reason `option_not_added` or `options_not_mutable` means the option cannot be removed. See [Polls and checklists](../polls-and-checklists.md) for poll updates.

### Example <a href="#example-2" id="example-2"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `POLL_MESSAGE_ID`, `POLL_OPTION_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const pollMessageId = process.env.POLL_MESSAGE_ID;
const pollOptionId = process.env.POLL_OPTION_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !pollMessageId || !pollOptionId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, POLL_MESSAGE_ID, POLL_OPTION_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.messagesDeletePollOption(chatId, pollMessageId, pollOptionId, { key: requestKey });
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
    result = bot.messages_delete_poll_option(os.environ["CHAT_ID"], os.environ["POLL_MESSAGE_ID"], os.environ["POLL_OPTION_ID"], key=os.environ["REQUEST_KEY"])
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
    let poll_message_id = std::env::var("POLL_MESSAGE_ID")?;
    let poll_option_id = std::env::var("POLL_OPTION_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.messages_delete_poll_option(&chat_id, &poll_message_id, &poll_option_id, &query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X DELETE \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/${POLL_MESSAGE_ID}/poll_options/${POLL_OPTION_ID}" \
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
    "allow_adding_options": true,
    "allows_multiple_answers": false,
    "allows_revoting": true,
    "hide_results_until_closes": false,
    "id": "00000000-0000-4000-8000-000000000001",
    "is_anonymous": false,
    "is_closed": false,
    "options": [
      {
        "id": "00000000-0000-4000-8000-000000000002",
        "text": {
          "entities": [],
          "text": "Yes"
        },
        "voter_count": 0
      },
      {
        "id": "00000000-0000-4000-8000-000000000003",
        "text": {
          "entities": [],
          "text": "No"
        },
        "voter_count": 0
      }
    ],
    "question": {
      "entities": [],
      "text": "Continue?"
    },
    "shuffle_options": false,
    "total_voter_count": 0,
    "type": "regular"
  }
}
```

## messages.stop_live_location <a href="#operation-messages-stop-live-location" id="operation-messages-stop-live-location"></a>

`POST /bot-api/v1/chats/{chat_id}/messages/{message_id}/stop_live_location`

Stop a live location sent by your bot before it expires. The returned message keeps the last coordinates and no longer contains active live-location settings.

Send `{}` to preserve its inline keyboard, or include a replacement keyboard. Omit `open_reply` in the replacement to preserve its accepted value. The target must be an active location authored by your bot.

### Parameters <a href="#parameters-3" id="parameters-3"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `message_id` | path | string | Yes | Use the ID of the message within this chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-3" id="request-3"></a>

Content type: `application/json`. You may omit the body.

[StopLiveLocation](types/stoplivelocation.md)

The optional inline keyboard replacement to apply when stopping live sharing. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `reply_markup` | [InputInlineKeyboard](types/inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |

### Response <a href="#response-3" id="response-3"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Message](types/message.md) in `result` |

### Errors <a href="#errors-3" id="errors-3"></a>

HTTP 404 `message_not_found` means the target is missing or inaccessible. HTTP 400 `invalid_parameter` can identify an inactive or invalid target. See [Locations and contacts](../locations-and-contacts.md) for live-location updates.

### Example <a href="#example-3" id="example-3"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `LIVE_LOCATION_MESSAGE_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const liveLocationMessageId = process.env.LIVE_LOCATION_MESSAGE_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !liveLocationMessageId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, LIVE_LOCATION_MESSAGE_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.StopLiveLocation = {};
  const result = await bot.messagesStopLiveLocation(chatId, liveLocationMessageId, { body, key: requestKey });
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
    body = models.StopLiveLocation()
    result = bot.messages_stop_live_location(os.environ["CHAT_ID"], os.environ["LIVE_LOCATION_MESSAGE_ID"], body=body, key=os.environ["REQUEST_KEY"])
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
    let live_location_message_id = std::env::var("LIVE_LOCATION_MESSAGE_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::StopLiveLocation {
        reply_markup: None,
    };
    let query = [];
    let result = bot.messages_stop_live_location(&chat_id, &live_location_message_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/${LIVE_LOCATION_MESSAGE_ID}/stop_live_location" \
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
    "chat": {
      "id": "4398046511200",
      "kind": "private"
    },
    "content": {
      "location": {
        "latitude": 14.6,
        "longitude": -90.5
      },
      "type": "location"
    },
    "fallback_text": "Location",
    "has_protected_content": false,
    "kind": "content",
    "message_id": "42",
    "revision": "1",
    "sender_user": {
      "id": "10000042",
      "is_bot": true,
      "name": "Acme Support",
      "username": "ACMESUPPORTBOT"
    },
    "sent_at": "2026-10-11T12:00:00Z",
    "silent": false,
    "state_version": "42"
  }
}
```

## messages.stop_poll <a href="#operation-messages-stop-poll" id="operation-messages-stop-poll"></a>

`POST /bot-api/v1/chats/{chat_id}/messages/{message_id}/stop_poll`

Close an open poll authored by your bot and return the final poll state.

Omit the body or send `{}` to keep the inline keyboard. You can instead supply a replacement inline keyboard, preserving its accepted `open_reply` value. Closing the poll also produces the final `poll` update. Reuse the accepted idempotency key to recover the same result after a lost response.

### Parameters <a href="#parameters-4" id="parameters-4"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | path | string | Yes | Use the ID of the chat available to your bot, encoded as a decimal string. This differs from the user ID. The value must match `^[1-9][0-9]*$`. |
| `message_id` | path | string | Yes | Use the ID of the message within this chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-4" id="request-4"></a>

Content type: `application/json`. You may omit the body.

[StopPoll](types/stoppoll.md)

The optional inline keyboard replacement to apply when closing a poll. Close an open poll authored by your bot. You can replace its inline keyboard while preserving open_reply. The poll closure and keyboard change succeed together. Reusing the idempotency key returns the final poll from the original request. A separate close request returns poll_closed. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `reply_markup` | [InputInlineKeyboard](types/inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |

### Response <a href="#response-4" id="response-4"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Poll](types/poll.md) in `result` |

### Errors <a href="#errors-4" id="errors-4"></a>

HTTP 403 `access_restricted` means another author owns the poll. HTTP 409 `poll_closed` means the poll is already closed. See [Polls and checklists](../polls-and-checklists.md) for closure and update delivery.

### Example <a href="#example-4" id="example-4"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `POLL_MESSAGE_ID`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const pollMessageId = process.env.POLL_MESSAGE_ID;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !chatId || !pollMessageId || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, POLL_MESSAGE_ID, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.StopPoll = {};
  const result = await bot.messagesStopPoll(chatId, pollMessageId, { body, key: requestKey });
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
    body = models.StopPoll()
    result = bot.messages_stop_poll(os.environ["CHAT_ID"], os.environ["POLL_MESSAGE_ID"], body=body, key=os.environ["REQUEST_KEY"])
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
    let poll_message_id = std::env::var("POLL_MESSAGE_ID")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::StopPoll {
        reply_markup: None,
    };
    let query = [];
    let result = bot.messages_stop_poll(&chat_id, &poll_message_id, &query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X POST \
  "https://signal.arx.pro/bot-api/v1/chats/${CHAT_ID}/messages/${POLL_MESSAGE_ID}/stop_poll" \
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
    "allow_adding_options": false,
    "allows_multiple_answers": false,
    "allows_revoting": true,
    "hide_results_until_closes": false,
    "id": "00000000-0000-4000-8000-000000000001",
    "is_anonymous": true,
    "is_closed": true,
    "options": [
      {
        "id": "00000000-0000-4000-8000-000000000002",
        "text": {
          "entities": [],
          "text": "Yes"
        },
        "voter_count": 0
      },
      {
        "id": "00000000-0000-4000-8000-000000000003",
        "text": {
          "entities": [],
          "text": "No"
        },
        "voter_count": 0
      }
    ],
    "question": {
      "entities": [],
      "text": "Continue?"
    },
    "shuffle_options": false,
    "total_voter_count": 0,
    "type": "regular"
  }
}
```

## message_effects.list <a href="#operation-message-effects-list" id="operation-message-effects-list"></a>

`GET /bot-api/v1/message_effects`

Read the catalog of message effects so your bot can choose a supported effect ID.

Choose an item whose `available_in` includes the destination chat kind. Pass its `effect_id` to `messages.send`, `messages.send_album`, `messages.copy`, or `messages.forward`. Treat IDs as opaque values. A recipient's app can ignore an effect it cannot display. See [Message effects](../message-effects.md) for usage.

An unknown or retired effect ID on a message request returns HTTP 400 `invalid_parameter` with reason `invalid_message_options`. Refresh the catalog and choose a current ID.

### Parameters <a href="#parameters-5" id="parameters-5"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-5" id="request-5"></a>

Do not send a request body.

### Response <a href="#response-5" id="response-5"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [MessageEffects](types/messageeffects.md) in `result` |

### Errors <a href="#errors-5" id="errors-5"></a>

HTTP 400 `invalid_request` means the catalog request included a query or body. Send this GET without either.

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
  const result = await bot.messageEffectsList();
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
    result = bot.message_effects_list()
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
    let result = bot.message_effects_list(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/message_effects" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "items": [
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
    ]
  }
}
```

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
