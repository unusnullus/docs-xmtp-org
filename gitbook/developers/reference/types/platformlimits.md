# PlatformLimits

The current messaging, file, update, and configuration limits reported by ARX.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `global_send_burst` | integer | Yes | This is the maximum burst of message units across all of your bot's chats. The minimum is 0. |
| `global_send_interval_microseconds` | integer | Yes | This is the refill interval for a global message unit, in microseconds. The minimum is 0. |
| `global_send_rate_per_second` | integer | Yes | This is the sustained send rate across all of your bot's chats, in message units per second. The minimum is 0. |
| `idempotency_receipt_seconds` | integer | Yes | This is how long an idempotency receipt is retained, in seconds. The minimum is 0. |
| `max_batch_bytes` | integer | Yes | This is the maximum serialized batch size, in bytes. The minimum is 0. |
| `max_bot_commands` | integer | Yes | This is the maximum number of commands in a command set. The minimum is 0. |
| `max_message_bytes` | integer | Yes | This is the maximum serialized message size, in bytes. The minimum is 0. |
| `max_page_content_bytes` | integer | Yes | This is the maximum content size in an update page, in bytes. The minimum is 0. |
| `max_polling_timeout_seconds` | integer | Yes | This is the maximum wait for one long polling request, in seconds. The minimum is 0. |
| `max_polling_updates` | integer | Yes | This is the maximum number of updates returned by one polling request. The minimum is 0. |
| `max_profile_localizations` | integer | Yes | This is the maximum number of localized profile entries. The minimum is 0. |
| `max_rich_blocks` | integer | Yes | This is the maximum number of blocks in one rich message. The minimum is 0. |
| `max_rich_columns` | integer | Yes | This is the maximum number of columns in a rich table. The minimum is 0. |
| `max_rich_depth` | integer | Yes | This is the maximum nesting depth of rich content. The minimum is 0. |
| `max_rich_media` | integer | Yes | This is the maximum number of media occurrences in one rich message. The minimum is 0. |
| `max_rich_utf16` | integer | Yes | This is the maximum text length of one rich message, in UTF-16 code units. The minimum is 0. |
| `max_text_utf16` | integer | Yes | This is the maximum text message length, in UTF-16 code units. The minimum is 0. |
| `private_send_burst` | integer | Yes | This is the maximum burst of send units in one private chat. The minimum is 0. |
| `private_send_interval_microseconds` | integer | Yes | This is the refill interval for a private chat send unit, in microseconds. The minimum is 0. |
| `private_send_rate_per_second` | integer | Yes | This is the sustained send rate in one private chat, in send units per second. The minimum is 0. |

Additional field names are allowed. Each value has the type integer. The minimum is 0.

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
