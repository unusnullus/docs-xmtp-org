---
description: "Read responses, preserve IDs and retry mutations without duplicating their effects."
---

# Making requests

Use the Bot API to read your bot's state and change its conversations. The SDKs build requests and unwrap successful responses for you. If you use HTTP directly, follow the operation's method, path and body in the [API reference](api.md).

## Send a request <a href="#send-a-request" id="send-a-request"></a>

Use the configured base URL, `https://signal.arx.pro/bot-api/v1`, with the operation path. Send JSON with `Content-Type: application/json`, except when an upload operation requires multipart data. Include your [bot token](authentication.md) in the Authorization header.

For example, send a reply to a chat that the user has started. Prepare `ARX_BOT_TOKEN` and `ARX_BOT_API_BASE_URL` with the [quickstart setup](quickstart.md#create-the-bot). Set `CHAT_ID` from `message.chat.id` in an incoming update, and save a `REQUEST_KEY` for this send:

```sh
curl --fail-with-body --max-time 30 -X POST \
  "$ARX_BOT_API_BASE_URL/chats/${CHAT_ID}/messages" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H 'Content-Type: application/json' \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  --data '{"content":{"type":"text","text":"Your note is saved."}}'
```

Use the [file upload guide](files.md) for multipart requests. Fields that appear in a response are not necessarily accepted in a request. Follow each operation's request schema and omit fields it does not define.

## Read a response <a href="#read-a-response" id="read-a-response"></a>

A successful JSON response has `ok: true` and the operation's value in `result`. For example, a successful mutation that returns a boolean uses this envelope:

```json
{"ok": true, "result": true}
```

An error has `ok: false` and an `error` object. The required fields are `code`, `message` and `request_id`:

```json
{
  "ok": false,
  "error": {
    "code": "invalid_parameter",
    "message": "invalid_parameter",
    "request_id": "01935f42-7b80-7a11-8b22-334455667788",
    "field": "content"
  }
}
```

Branch on `error.code` and retain `error.request_id` when you need support. Optional fields can identify the invalid field, retry delay or affected scope. Check HTTP status as well as the envelope. [Errors](errors.md) lists the codes and recovery steps.

File downloads return bytes and accept a `Range` header. Use an SDK download method or follow the [files reference](reference/files.md). The `openapi.get` response is the OpenAPI JSON document itself, without the `ok` and `result` envelope.

## Preserve IDs <a href="#preserve-ids" id="preserve-ids"></a>

Keep user IDs, chat IDs, message IDs and update IDs as strings. A user ID identifies an account; a chat ID identifies its conversation with your bot. They are not interchangeable. File IDs and pagination cursors are opaque strings: store them as received rather than parsing or modifying them.

Polling offsets need exact integer arithmetic on `update_id`. Python integers, JavaScript `BigInt`, and Rust's checked integer arithmetic avoid rounding when calculating the next offset. Convert the result back to a string before sending it, as shown in the [quickstart](quickstart.md).

## Idempotency <a href="#idempotency" id="idempotency"></a>

For an operation that accepts `Idempotency-Key`, choose one key per logical action. A key contains 1 to 64 ASCII letters, digits, underscores or hyphens. Persist the key with the request payload before sending important work.

Repeat the same key and payload when retrying that action. A completed request replays its saved result. A different payload with the same key returns `idempotency_key_reused`; `idempotency_in_progress` means you should wait and retry with that same key.

The saved replay result lasts 24 hours. After it expires, `idempotency_key_expired` means you must reconcile the original action before deciding whether to send another request. An idempotency key does not make your database writes or external service calls safe to repeat. Handle those effects separately, and [deduplicate incoming updates](updates.md).

The SDKs create keys for supported mutations and retain them on automatic retries. Supply your own stable key when your application needs to resume after a restart. See [SDK retries](sdks.md#retries-and-idempotency).

## PATCH: omission, null and empty values <a href="#patch-omission-null-and-empty-values" id="patch-omission-null-and-empty-values"></a>

A PATCH request changes only the fields you send. Omission preserves a field. When a field is nullable, `null` can clear it; an empty string has the meaning specified by that operation.

For example, `profile.update` with `{"description": null}` clears the default description and leaves other fields unchanged. Its optional descriptions also accept an empty string for clearing. The default name must remain nonblank, while a localized name can be cleared. Rust's `Presence::Unset` and `Presence::Null` preserve the difference between omission and null.

Check the [profile reference](reference/profile.md) before updating fields. To turn responses into a working handler, continue with [Receiving updates](receiving-updates.md).
