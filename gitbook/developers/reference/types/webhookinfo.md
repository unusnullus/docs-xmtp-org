# WebhookInfo

Your bot's delivery mode, saved webhook settings, queue size, and recent delivery errors.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `allowed_updates` | Array of string | Yes | The saved filter for update delivery. An empty list uses the default filter. The update types currently delivered are message, edited_message, callback_query, poll, poll_answer, my_chat_member, stopped_message_generation, messages_deleted, message_reaction, and updates_dropped. messages_deleted and message_reaction require opt-in. chat_member and message_reaction_count also require opt-in but do not currently produce updates. Other accepted update type names do not currently produce events. The value default selects the default set alongside any explicitly selected types. You always receive updates_dropped, regardless of the filter. |
| `delivery_epoch` | string | Yes | This is the version of update delivery, encoded as a decimal string. It changes when delivery is reconfigured or invalidated, such as after token revocation or an ownership transfer. You can usually ignore it. The value must match `^[1-9][0-9]*$`. |
| `delivery_paused_reason` | `"token_revoked"` or `"owner_paused"` or `"circuit_open"` or `"bot_suspended"` | No | The reason delivery is paused. token_revoked means a new token is needed; owner_paused means the bot owner paused delivery; bot_suspended means the bot is suspended. circuit_open means repeated webhook failures have temporarily delayed delivery. ARX sends probes automatically and resumes delivery after a successful probe. |
| `dropped_update_count_24h` | integer | Yes | This is the number of updates dropped in the last 24 hours. The minimum is 0. |
| `has_custom_certificate` | boolean | Yes | This is true when a custom certificate is configured for the webhook. |
| `ip_address` | string or null | Yes | The configured IPv4 or IPv6 address used to connect to the webhook, or null when no fixed address is configured. |
| `last_dropped_at` | string | No | This records when updates were most recently dropped, in RFC 3339 format. The format is `date-time`. |
| `last_error_at` | string | No | This records the date and time of the most recent webhook delivery error, in RFC 3339 format. The format is `date-time`. |
| `last_error_message` | string | No | This describes the most recent webhook delivery error. The maximum length is 128 characters. |
| `last_reply_error_at` | string | No | This records the date and time of the most recent webhook reply error, in RFC 3339 format. The format is `date-time`. |
| `last_reply_error_message` | string | No | This describes the most recent webhook reply error. The maximum length is 128 characters. |
| `last_sync_error_at` | string | No | This records the date and time of the most recent update synchronization error, in RFC 3339 format. The format is `date-time`. |
| `max_connections` | integer | Yes | This is the maximum number of simultaneous connections used to deliver webhook updates. The minimum is 1. The maximum is 100. |
| `mode` | `"polling"` or `"webhook"` or `"none"` or `"internal"` | Yes | Your bot's delivery mode: polling, webhook, or none when delivery is disabled. internal identifies an ARX-managed official bot whose delivery you cannot reconfigure. |
| `oldest_pending_at` | string or null | Yes | This records when the oldest queued update was created, or null when the queue is empty. The format is `date-time`. |
| `pending_update_count` | integer | Yes | This is the number of updates waiting for delivery. The minimum is 0. |
| `reply_operations_enabled` | boolean | Yes | This is true when your webhook may return an API operation in its response body. |
| `signing_key_generation` | string | Yes | This is the current webhook signing key generation, encoded as a decimal string. It increases when the bot token is rotated or revoked, or ownership changes. Use it to distinguish signing keys across token changes. The value must match `^[1-9][0-9]*$`. |
| `url` | string or null | Yes | The configured HTTPS webhook URL, or null when no webhook endpoint is configured. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
