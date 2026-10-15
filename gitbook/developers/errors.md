---
description: "Read Bot API error codes, decide what to fix and retry safely."
---

# Errors

You can decide how to recover from a failed request by reading `error.code` and the HTTP status. Use the code in your program, because the English `error.message` can change. Keep `request_id` when asking for support, without including your token or message content.

```json
{
  "ok": false,
  "error": {
    "code": "rate_limited",
    "message": "Please retry later",
    "request_id": "019a0ff4-12c0-7000-8000-000000000001",
    "retry_after": 3,
    "scope": "chat"
  }
}
```

The required error fields are `code`, `message` and `request_id`. Optional fields include `field`, `reason`, `details`, `dependency`, `retry_after`, `retry_after_ms` and `scope`. `field` identifies the affected input. `details` can be an object, an array of diagnostic objects or null. Read only the fields you need and allow additional diagnostic fields.

This catalog covers the 71 Bot API codes. NexoCreator reports account and device-approval problems in its own chat; your bot never receives them. The HTTP statuses below are the Bot API statuses.

## Request and content errors <a href="#request-and-content-errors" id="request-and-content-errors"></a>

| Code | HTTP | What to do |
| --- | --- | --- |
| `invalid_request` | 400 | Correct the JSON, multipart structure or request parameters. Use the operation's required fields. |
| `invalid_parameter` | 400 | Use `field`, `reason` and `details` to correct the value or field combination. |
| `name_not_allowed` | 400 | Choose a display name that meets the profile rules and does not impersonate a protected identity. |
| `unsupported_content` | 400 | Choose a content type and options supported by the operation and chat. |
| `payload_too_large` | 400 | Reduce the encoded message or batch, including reply context and metadata. See [payload limits](limits.md#files-and-request-bodies). |
| `request_too_large` | 413 | Reduce the JSON or complete multipart body. This limit is separate from individual file limits. |
| `quote_not_found` | 400 | Match the source quote's text, entities and position, or omit the quote. |
| `poll_option_invalid` | 400 | Read the current poll and select an option that still exists. |
| `request_not_valid` | 400 | Read the current source message and use a valid request or checklist-task reference. |
| `reaction_not_allowed` | 400 | Choose a currently allowed reaction from `platform.get` and respect the per-message reaction limit. |
| `album_type_mismatch` | 400 | Use a permitted album combination. Audio and document albums each contain only their own type. |
| `message_not_editable` | 400 | Read the restriction in `reason`. Check message authorship and which fields can be edited. |

An identical edit returns the current message without a new revision. It does not produce a separate error.

## Authentication and access <a href="#authentication-and-access" id="authentication-and-access"></a>

| Code | HTTP | What to do |
| --- | --- | --- |
| `unauthorized` | 401 | Check your environment and whether the bot token is missing, malformed, invalid or revoked. Replace it through NexoCreator if necessary. |
| `bot_suspended` | 403 | Pause your workers and review the suspension in NexoCreator. Repeated requests will not restore access. |
| `bot_unavailable` | 403 | Stop the affected work and inspect the bot's lifecycle state in NexoCreator. |
| `bot_deleted` | 410 | Stop processing for this bot. Its deletion is permanent. |
| `owner_not_eligible` | 403 | Resolve the account eligibility requirement in NexoCreator before repeating the profile action. |
| `capability_not_available` | 403 | Check `details.capability` and `reason`, then use an available feature. See [Not available yet](not-available-yet.md). |
| `chat_not_started` | 403 | Wait for the user to start the bot's private chat. Do not retry unsolicited sends. |
| `bot_blocked_by_user` | 403 | Stop sending to this user until they choose to interact with the bot again. |
| `chat_blocked` | 403 | Stop the action in the blocked chat. Do not keep retrying it. |
| `access_restricted` | 403 | Recheck access to the selected resource. Wait for the relevant access restriction to change. |
| `content_not_allowed` | 403 | Remove the disallowed content or select a permitted action. |
| `bot_not_startable` | 403 | Check whether the selected bot allows the requested start action. |
| `user_deactivated` | 403 | Stop work for the inactive account. |
| `protected_content` | 403 | Do not copy or forward the protected source. |
| `message_too_old` | 403 | The message is outside the permitted deletion window. Leave it unchanged. |
| `message_cant_be_deleted` | 403 | Inspect the source and deletion rules. Another restriction prevents deletion. |
| `checklist_not_editable` | 403 | Respect the checklist's permissions and editability rules. |
| `contact_not_shareable` | 403 | Obtain the required sharing consent or send a contact the user is allowed to share. |

Your bot cannot enumerate users or infer resource existence from these responses. An inaccessible resource can return the same not-found code as a nonexistent one.

## Resources and routes <a href="#resources-and-routes" id="resources-and-routes"></a>

| Code | HTTP | What to do |
| --- | --- | --- |
| `bot_not_found` | 404 | Check that you are using the intended bot's token. |
| `chat_not_found` | 404 | Use a chat ID readable by this bot. |
| `message_not_found` | 404 | Use a message that remains readable in the selected chat. |
| `user_not_found` | 404 | Use a user already known and accessible to the bot. |
| `draft_not_found` | 404 | The draft is unavailable. Check whether it expired, stopped or was replaced before continuing generation. |
| `unknown_operation` | 404 | Check your API base URL and operation path against the reference. |
| `route_not_found` | 404 | Correct the request path. |
| `method_not_allowed` | 405 | Use the HTTP method specified for the route. |

## Files and uploads <a href="#files-and-uploads" id="files-and-uploads"></a>

| Code | HTTP | What to do |
| --- | --- | --- |
| `file_not_found` | 404 | Check file access, expiry and bot scope. Obtain a current file ID from an accessible source when possible. |
| `file_too_large` | 413 | Choose a smaller file. `details.limit` gives the maximum bytes for that media type. |
| `file_type_not_allowed` | 400 | Use an accepted container, codec or file type. Renaming the file will not fix its contents. |
| `file_type_mismatch` | 400 | Use the file with the media kind it was validated for. |
| `file_not_ready` | 409 | Poll metadata with backoff, or use the SDK's readiness helper, before sending it. |
| `file_quota_exceeded` | 429 | Check the bot's library usage. Waiting alone may not free space, so avoid repeatedly uploading the same rejected file. |
| `file_capacity_exhausted` | 429 | Wait for the requested delay and retry within a bounded policy. |
| `download_too_large` | 400 | Use metadata-only lookup or a smaller original. Requesting a byte range does not bypass the full-file size limit. |
| `url_fetch_failed` | 400 | Check `reason`, the public URL and its response. Correct the source before retrying a validation failure. |
| `upload_expired` | 410 | Start a new upload for the expired upload session, after checking the result of the old one. |
| `upload_not_complete` | 409 | Finish supplying the required upload bytes before completing it. |
| `upload_in_progress` | 409 | Wait for the current upload attempt and check its result before starting another. |
| `upload_not_allowed` | 403 | Recheck upload permission and the bot or file state. |

[Files and media](files.md) explains input sources, processing and download access. An unsupported or unsatisfiable download range returns HTTP 416 with an empty body and `Content-Range`, instead of the JSON error envelope.

## Conflicts and callback deadlines <a href="#conflicts-and-callback-deadlines" id="conflicts-and-callback-deadlines"></a>

| Code | HTTP | What to do |
| --- | --- | --- |
| `version_conflict` | 409 | Reload the current resource before applying a new change. For drafts, `reason: "draft_retired"` means you must stop using that draft ID. |
| `revision_conflict` | 409 | Reconcile the current message revision before trying a new edit. |
| `operator_changed` | 409 | The bot owner changed during the interaction. Reconcile the handover and ask the user to repeat the action when appropriate. |
| `idempotency_key_reused` | 409 | Restore the original body and file bytes. Use a new key only for a new action. |
| `idempotency_in_progress` | 409 | The original request is still running. Wait and repeat the identical request with its original key. |
| `idempotency_key_expired` | 409 | The replay body expired. Reconcile `details.result_ref` when supplied before considering a new action. |
| `polling_conflict` | 409 | Stop duplicate pollers, preserve your confirmed offset and honor the wait before polling again. |
| `delivery_mode_conflict` | 409 | Check the current transport and restart the appropriate consumer for that mode. |
| `delivery_paused` | 409 | Inspect `/delivery` in NexoCreator and resume only when the bot and credentials are ready. |
| `webhook_active` | 409 | Delete the webhook before switching to polling. |
| `poll_closed` | 409 | Read the final poll state and leave the closed poll unchanged. |
| `pin_limit_reached` | 409 | Unpin a message before pinning another one. |
| `query_already_answered` | 409 | Keep the original callback answer. Do not attempt a different answer. |
| `query_expired` | 410 | The callback answer window has closed. Ask the user to press the button again if the action is still relevant. |
| `callback_expired` | 410 | The callback context is no longer valid. Ask the user to open current controls. |
| `cursor_expired` | 410 | Restart pagination instead of reusing the old cursor. |
| `snapshot_expired` | 410 | Restart the paginated view to read current results. |

Do not treat `draft_retired` as an error code. It is a reason attached to `version_conflict`. See [Drafts](drafts.md) for stop and retirement behavior, and [Lifecycle](lifecycle.md) for handover recovery.

## Temporary failures <a href="#temporary-failures" id="temporary-failures"></a>

| Code | HTTP | What to do |
| --- | --- | --- |
| `rate_limited` | 429 | Wait for the requested delay and pace the affected scope. |
| `upstream_unavailable` | 503 | Honor `Retry-After` when present and use bounded retries with the original idempotency key. |
| `timeout` | 504 | The outcome may be uncertain. Keep the original idempotency key and reconcile before initiating another action. |

## Retries and SDK behavior <a href="#retries-and-sdk-behavior" id="retries-and-sdk-behavior"></a>

The TypeScript, Python and Rust SDKs retry any call after transport failures, HTTP 429, HTTP 5xx, `polling_conflict` and `idempotency_in_progress`. For a write, the SDK generates an idempotency key if you do not pass one. It keeps the same key and body for retries within that call. Ordinary validation, permission and stale-resource errors return to your program for a decision.

The default is three attempts in total. The SDK's exponential backoff starts at 0.2 seconds and caps at five seconds, with up to 25% jitter. A longer server-requested wait takes precedence over that backoff. The effective delay uses the greatest available value from `Retry-After`, `retry_after` and `retry_after_ms`. Attempts must fit within the operation deadline.

| Setting | TypeScript | Python | Rust |
| --- | --- | --- | --- |
| Attempt limit | `maxAttempts` | `max_attempts` | `max_attempts` |
| Initial backoff | `baseDelaySeconds` | `base_delay_seconds` | `base_delay` |
| Maximum backoff | `maxDelaySeconds` | `max_delay_seconds` | `max_delay` |

Multipart retries also require replayable files. Immutable byte sources can be replayed automatically. File paths and streams are not automatically replayable, because the SDK cannot promise that reopening or rereading them produces the same bytes. If you retry these yourself, preserve the original files, body and key.

Persist a stable key before a business action if you need to recover across process restarts. A new invocation without the original key can become a new action even if its body looks identical. After `idempotency_key_expired`, inspect the returned result reference and your own records instead of repeatedly choosing new keys.

For pacing and payload sizes, continue with [Limits](limits.md). For request construction and idempotency, read [Making requests](making-requests.md).
