---
description: "Check effective sending allowances, payload sizes and time windows."
---

# Limits and rate limits

You can pace your bot and reject oversized input before sending a request. Read `platform.get` for the bot's effective limits. The values below are defaults. The intervals and bursts that `platform.get` returns are the policy applied to your bot now.

## Effective sending policy <a href="#effective-sending-policy" id="effective-sending-policy"></a>

`platform.get.send_profile` is `standard` or `restricted`. Its `limits` object contains nonnegative integers, including these sending values:

| Field | How to use it |
| --- | --- |
| `private_send_interval_microseconds` | Space sends to each private chat by this interval. |
| `global_send_interval_microseconds` | Space message units across the bot by this interval. |
| `private_send_burst` | Limit the initial burst in one private chat. |
| `global_send_burst` | Limit the initial burst across the bot. |
| `private_send_rate_per_second`, `global_send_rate_per_second` | Treat these as rounded-up estimates. Use the intervals for precise scheduling. |

For example, an interval of 2,000,000 microseconds allows one send every two seconds, even though the rounded rate is 1. A positive interval still permits sending at rates below one message per second.

A restricted bot can continue in existing chats with lower allowances. It is hidden from discovery and cannot begin new chats. Refresh the effective limits after a policy change and honor HTTP 429 waits. Suspension has different effects, covered in [Security](security.md#respond-to-suspension).

## Sending and request rates <a href="#sending-and-request-rates" id="sending-and-request-rates"></a>

| Operation | Default allowance | Burst or additional rule |
| --- | --- | --- |
| All requests | 300 requests per second per bot. | Burst of 600. |
| Private messages | One message per second in each chat. | Five per chat. |
| Global messages | 30 message units per second across the bot. | 120 units. |
| Chat operations other than sends, such as edits, deletions and history reads | 60 requests per second across the bot. | 120 requests. Edits other than live-location updates also allow one per second in each chat, with a burst of five. |
| History reads (`messages.history`, `messages.get`) | Five per second per bot. | Burst of 20. |
| Draft changes | Five changes per second in each chat. | Coalesce intermediate changes before sending. |
| Chat actions | One per second in each chat. | Repeated actions within the interval are combined. |
| Polling | Ten requests per second per bot. | Burst of 20. Run one poller. |
| Configuration changes | 20 per minute per bot. | Burst of 20 shared by configuration operations. |
| Webhook test | One every ten seconds per bot. | Wait before testing again. |

A message unit is one message sent by your bot. An album or forward or copy batch uses one global message unit per item, while the whole batch costs one unit in the destination chat. A batch that does not fit the global allowance is rejected without partially consuming that allowance. File transfers can also return 429 when concurrency is exhausted. Queue transfers and use the returned wait instead of starting more of them.

## Handle a rate limit <a href="#handle-a-rate-limit" id="handle-a-rate-limit"></a>

On HTTP 429, read `error.scope`, or `error.details.scope` when the top-level field is absent, and wait at least as long as the server requests. Use the greatest available value among `Retry-After`, `error.retry_after` in seconds and `error.retry_after_ms` converted to seconds, then add jitter. Pace each chat separately while respecting the bot's global interval.

The `scope` identifies which allowance you reached:

| Scope | What to pace |
| --- | --- |
| `chat` | Messages or changes in the affected chat. |
| `bot` | Requests or message units across your bot. |
| `ip` | Requests from your service's IP address. |
| `authentication` | Token-authentication attempts. |
| `operation` | Polling requests. |
| `configuration` | Changes to your bot's profile, commands or delivery settings. |
| `upload` | Concurrent uploads. |
| `file` | File work, including library quota or temporary capacity limits. |

Some limits, such as `webhook.test`, return no scope. Honor the wait anyway. If you receive another scope value, still honor the wait and retain it in your diagnostics.

Retry a change with its original idempotency key, body and file bytes. The SDKs already retry eligible failures within their attempt and deadline limits. Avoid wrapping them in an unlimited retry loop. [Errors](errors.md#retries-and-sdk-behavior) explains which failures are retried and how to handle an uncertain result.

## Text and structured content <a href="#text-and-structured-content" id="text-and-structured-content"></a>

Text sizes use UTF-16 units unless a row specifies bytes or characters. An emoji can occupy more than one unit. Validate the parsed content, including entities and rich blocks, rather than counting source markup characters.

| Content | Default limit |
| --- | --- |
| Message text | 1 to 4,096 units. |
| Draft text | 0 to 4,096 units. |
| Caption or quote | Up to 1,024 units each. |
| Rich message | 32,768 units, 500 blocks, 16 nesting levels, 50 media occurrences and 20 table columns. |
| Poll | A question of 1 to 300 units and 1 to 12 options of 1 to 100 units each. |
| Poll description | Up to 1,024 units. |
| Quiz explanation | Up to 200 units and two line breaks. |
| Checklist | A title of 1 to 255 units and 1 to 30 tasks, each with 1 to 100 units of text. |
| Keyboard | Up to 100 buttons, eight per row and 1 to 64 units per label. |
| Callback data | 1 to 64 UTF-8 bytes. |
| Idempotency key | 1 to 64 ASCII letters, digits, underscores or hyphens. |

See [Formatting](formatting.md), [Keyboards](keyboards.md) and [Polls and checklists](polls-and-checklists.md) for field combinations and examples.

## Files and request bodies <a href="#files-and-request-bodies" id="files-and-request-bodies"></a>

One KiB is 1,024 bytes, and one MiB is 1,048,576 bytes. These limits use binary units.

| Resource | Default limit |
| --- | --- |
| JSON request body or multipart JSON payload | 1 MiB (1,048,576 bytes). |
| Entire multipart HTTP body, including overhead | 51 MiB (53,477,376 bytes). |
| File parts in a message send or edit | 32 binary parts plus the JSON payload. |
| File parts in an album | 30 binary parts plus the JSON payload. |
| Photo or live-photo video upload | 10 MiB (10,485,760 bytes). |
| Other media upload | 50 MiB (52,428,800 bytes). Thumbnails have a smaller limit. |
| Photo fetched by URL | 5 MiB (5,242,880 bytes). |
| Other media fetched by URL | 20 MiB (20,971,520 bytes), where that media type accepts URLs. |
| Sticker upload | 512 KiB (524,288 bytes) for a static WebP sticker, 256 KiB for a WebM video sticker and 64 KiB compressed for a TGS animated sticker. See [Stickers and dice](messages/stickers-and-dice.md#sticker-formats). |
| Bot download | 20 MiB (20,971,520 bytes) for the complete original, including requests for a byte range. |
| Bot file library | 5 GiB (5,368,709,120 bytes). |
| Encoded message | 786,432 bytes (768 KiB), including its reply and service context. |
| Forward or copy batch | 983,040 bytes (960 KiB). |
| Polling update page content | 2,031,616 bytes (1,984 KiB). Total size of the updates returned in one `updates.get` response. |

Media bytes do not count toward the encoded message budget. File metadata and reply context do, so a message can exceed its byte budget even when its text is within the character limit. `payload_too_large` returns HTTP 400. Request-body overflow returns HTTP 413 `request_too_large`, while an oversized file returns HTTP 413 `file_too_large`.

Use [Files and media](files.md) for the full table of types, MIME values, thumbnails, profile media and retention rules.

## Collections and time windows <a href="#collections-and-time-windows" id="collections-and-time-windows"></a>

| Collection or lifetime | Default bound |
| --- | --- |
| Album | 2 to 10 items. |
| Forward, copy or delete batch | Up to 100 message IDs. |
| Pinned messages in a private chat | 1,000. |
| Polling result | 1 to 100 updates requested. |
| Long-poll timeout | 0 to 50 seconds. Set your client's request deadline above the chosen wait. |
| Poll opening period or future close | 5 to 2,628,000 seconds (about 30 days). |
| Durable update retention | 24 hours. |
| Callback answer window | 15 minutes. |
| Draft lifetime | 30 seconds after its last change. |
| Activity indicator | Up to five seconds. |
| Idempotency response | 24 hours. |
| Compact idempotency receipt | Seven days. |

After the response body expires, `idempotency_key_expired` can include `details.result_ref`. Reconcile that result before deciding whether a new action is necessary. Do not choose a fresh key to bypass an expired receipt. See [Making requests](making-requests.md).

## Webhooks and update delivery <a href="#webhooks-and-update-delivery" id="webhooks-and-update-delivery"></a>

| Setting or behavior | Limit |
| --- | --- |
| Webhook URL | HTTPS on port 443, 80, 88 or 8443, up to 2,048 bytes. |
| Simultaneous webhook requests (`max_connections`) | 1 to 100. The default is 40. |
| Webhook `secret_token` | 1 to 256 ASCII letters, digits, underscores or hyphens. |
| Custom webhook certificate | One PEM X.509 certificate of up to 16 KiB. |
| Webhook response deadline | 30 seconds, with 5 seconds to connect. |
| Webhook response body with an operation | Up to 1 MiB. A larger body still acknowledges the update. |
| `Retry-After` honored from your receiver | 1 second to 1 hour. |
| Signature timestamp tolerance in the SDK verifiers | 300 seconds. |
| Pending updates per bot | 1,000,000. Older unacknowledged updates are dropped beyond this. |
| Update retention | 24 hours. |
| `allowed_updates` in a polling query | Up to 2,048 bytes. |

[Polling and webhooks](updates.md#acknowledgements-and-retries) explains retry timing and what happens when a receiver keeps failing.

## Groups and topics <a href="#groups-and-topics" id="groups-and-topics"></a>

| Property | Limit |
| --- | --- |
| Group title | 1 to 128 characters. |
| Group description | Up to 255 characters. |
| Topic name | 1 to 128 characters. |
| Administrator custom title | Up to 16 characters, without emoji or control characters. |
| Invitation member limit | 1 to 99,999 concurrent members. It cannot be combined with join requests. |
| Member restriction duration | 30 seconds to 366 days. A shorter, longer or omitted duration makes the restriction permanent. |
| History page (`messages.history`) | 1 to 100 messages, 50 by default. |
| Bots in one group | 20. |
| Administrators in one group | 50. |
| Pinned messages in one group | 1,000. |
| Active invite links | 1,000 per creator in each group. |
| Active topics in one forum | 10,000. |
| Pending join request | 30 days, then it expires. |
| Bot deletion window in a group | 48 hours after the message was sent, including your bot's own messages. |

The group's member permissions also apply to your bot. A send of a content type the group does not allow fails with `send_permission_denied`, and `details.permission` names it. Slow mode and anti-spam apply to people, not to bots. See [Errors](errors.md#groups-channels-and-topics).

## Bot profile and ownership <a href="#bot-profile-and-ownership" id="bot-profile-and-ownership"></a>

| Property | Default bound |
| --- | --- |
| Handle | 8 to 24 ASCII letters or digits, normalized to uppercase, ending in `BOT`; test bots end in `TESTBOT`. |
| Default display name | 1 to 64 UTF-16 units. |
| Description | Up to 512 UTF-16 units. |
| Short description | Up to 120 UTF-16 units. |
| Localized profile variants | Up to 1,000. |
| Commands in each scope and language | Up to 100. Command names use 1 to 32 lowercase ASCII letters, digits or underscores, without `/`. Descriptions use 1 to 256 UTF-16 units. |
| Bot ownership | 20 regular bots and ten test bots for your account. |
| Creation rate | One bot per minute and ten per day. |
| Minimum account age for creation | 24 hours. |
| Testers | Up to 50 per test bot. |

These are the default creation allowances and profile localization limits. For the workflows that enforce them, continue with [NexoCreator](nexocreator.md). For the operation fields, use the [API reference](api.md).
