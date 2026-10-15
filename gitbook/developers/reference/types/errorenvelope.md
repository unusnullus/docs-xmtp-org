# ErrorEnvelope

A failed API request. ok is always false; error contains the code, message, and request identifier.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `error` | object | Yes | This describes a failed request or webhook test. |
| `error.code` | `"unauthorized"` or `"unknown_operation"` or `"route_not_found"` or `"method_not_allowed"` or `"request_too_large"` or `"invalid_request"` or `"rate_limited"` or `"bot_not_found"` or `"name_not_allowed"` or `"bot_suspended"` or `"bot_deleted"` or `"owner_not_eligible"` or `"capability_not_available"` or `"draft_not_found"` or `"version_conflict"` or `"chat_not_found"` or `"chat_not_started"` or `"bot_blocked_by_user"` or `"bot_unavailable"` or `"access_restricted"` or `"operator_changed"` or `"content_not_allowed"` or `"idempotency_key_reused"` or `"idempotency_key_expired"` or `"revision_conflict"` or `"message_not_found"` or `"callback_expired"` or `"cursor_expired"` or `"snapshot_expired"` or `"file_too_large"` or `"file_type_not_allowed"` or `"upload_expired"` or `"file_not_ready"` or `"poll_closed"` or `"poll_option_invalid"` or `"checklist_not_editable"` or `"request_not_valid"` or `"contact_not_shareable"` or `"file_not_found"` or `"bot_not_startable"` or `"protected_content"` or `"reaction_not_allowed"` or `"message_cant_be_deleted"` or `"message_not_editable"` or `"pin_limit_reached"` or `"invalid_parameter"` or `"file_type_mismatch"` or `"file_capacity_exhausted"` or `"file_quota_exceeded"` or `"idempotency_in_progress"` or `"upload_not_complete"` or `"upload_in_progress"` or `"album_type_mismatch"` or `"upload_not_allowed"` or `"chat_blocked"` or `"unsupported_content"` or `"payload_too_large"` or `"message_too_old"` or `"download_too_large"` or `"url_fetch_failed"` or `"polling_conflict"` or `"delivery_mode_conflict"` or `"query_expired"` or `"query_already_answered"` or `"quote_not_found"` or `"delivery_paused"` or `"webhook_active"` or `"user_not_found"` or `"user_deactivated"` or `"timeout"` or `"upstream_unavailable"` | Yes | Use this stable error code to decide how to handle a failure. |
| `error.dependency` | string | No | A diagnostic label you can include with request_id when reporting the error. |
| `error.details` | object or array or null | No | This provides additional error information. Its shape depends on the error code. |
| `error.details.capability` | string | No | This identifies the capability associated with the error. |
| `error.details.field` | string | No | This identifies the request field associated with the error. |
| `error.details.limit` | string or integer | No | This is the applicable limit reported by the error. |
| `error.details.offset` | integer | No | This is the start of this entity in UTF-16 code units, or the position associated with an error. |
| `error.details.operator_epoch` | string | No | The current ownership version reported with operator_changed, encoded as a decimal string. You can usually ignore this diagnostic value. Refresh your bot state before deciding whether to retry. The value must match `^[0-9]+$`. |
| `error.details.product` | string | No | This identifies the product associated with an unavailable capability. |
| `error.details.reason` | string | No | This gives a more specific cause for the error code, such as draft_retired for version_conflict. |
| `error.details.result_ref` | object | No | These identifiers refer to a previously completed result associated with the idempotency receipt. The response includes at least one of `file_id` or `chat_id`. |
| `error.details.result_ref.chat_id` | string | No | This is the chat ID, encoded as a decimal string. A private chat ID differs from the user's ID. The value must match `^[1-9][0-9]*$`. |
| `error.details.result_ref.file_id` | string | No | Your bot can use this opaque identifier to send or retrieve the file. |
| `error.details.result_ref.message_id` | string | No | This is the message ID within its chat, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `error.details.result_ref.message_ids` | Array of string | No | These are message IDs within the specified chat, encoded as decimal strings. |
| `error.details.result_ref.message_ids[]` | string | Per item | The value must match `^[1-9][0-9]*$`. |
| `error.field` | string | No | This identifies the request field associated with the error. |
| `error.message` | string | Yes | This explains the error. Use code to handle errors programmatically. |
| `error.reason` | string | No | This gives a more specific cause for the error code, such as draft_retired for version_conflict. |
| `error.request_id` | string | Yes | You can include this request identifier when asking for support. |
| `error.retry_after` | integer | No | This is the time to wait before retrying, in seconds. The minimum is 1. |
| `error.retry_after_ms` | integer | No | This is the time to wait before retrying, in milliseconds. The minimum is 1. |
| `error.scope` | string | No | This is the scope of the command set, or scope affected by a rate limit error. |
| `ok` | `false` | Yes | Always false for an error response. Read error.code to decide how to handle the failure. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
