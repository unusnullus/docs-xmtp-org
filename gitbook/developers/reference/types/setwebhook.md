# SetWebhook

The HTTPS endpoint, verification settings, and update filter for webhook delivery. Use a public HTTPS endpoint on port 443, 80, 88, or 8443. The URL cannot contain credentials or a fragment, and redirects are not followed. The hostname must resolve to a public address. To use a custom certificate, send one PEM X.509 certificate of at most 16 KiB in a multipart request. It replaces the default certificate trust roots. Your endpoint does not have to be reachable when you save this configuration.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `allowed_updates` | Array of `"message"` or `"edited_message"` or `"channel_post"` or `"edited_channel_post"` or `"callback_query"` or `"inline_query"` or `"chosen_inline_result"` or `"shipping_query"` or `"pre_checkout_query"` or `"purchased_paid_media"` or `"subscription_updated"` or `"refund_request_updated"` or `"login_grant_revoked"` or `"poll"` or `"poll_answer"` or `"my_chat_member"` or `"chat_join_request"` or `"stopped_message_generation"` or `"managed_bot"` or `"guest_message"` or `"business_connection"` or `"business_envelope"` or `"business_transfer_updated"` or `"gift_purchase_updated"` or `"chat_boost"` or `"removed_chat_boost"` or `"default"` or `"updates_dropped"` or `"messages_deleted"` or `"chat_member"` or `"message_reaction"` or `"message_reaction_count"` | No | Select the update types to receive. Omit this field to keep the saved filter, or send an empty list to restore the default filter. The update types currently delivered are message, edited_message, callback_query, poll, poll_answer, my_chat_member, stopped_message_generation, messages_deleted, message_reaction, and updates_dropped. messages_deleted and message_reaction require opt-in. chat_member and message_reaction_count also require opt-in but do not currently produce updates. Other accepted update type names do not currently produce events. The value default selects the default set alongside any explicitly selected types. You always receive updates_dropped, regardless of the filter. Filter changes apply to new events. |
| `certificate` | object | No | Use this custom PEM X.509 certificate to verify your webhook server. Only the listed fields are allowed. |
| `certificate.attach` | string | Yes | This is the name of the binary part in the same multipart request. Use the part name without an attach:// prefix. The value must match `^[A-Za-z0-9_-]{1,64}$`. |
| `drop_pending_updates` | boolean | No | Set this to true to discard queued updates when applying this delivery change. The default is `false`. |
| `ip_address` | IPv4 or IPv6 address | No | A fixed public IPv4 or IPv6 address to connect to instead of resolving the webhook hostname. |
| `max_connections` | integer | No | This is the maximum number of simultaneous connections used to deliver webhook updates. The minimum is 1. The maximum is 100. The default is `40`. |
| `reply_operations_enabled` | boolean | No | This is true when your webhook may return an API operation in its response body. The default is `false`. |
| `secret_token` | string | No | The platform sends this secret in the Arx-Bot-Api-Secret-Token header on webhook deliveries. The value must match `^[A-Za-z0-9_-]{1,256}$`. |
| `url` | string | Yes | Set the public HTTPS endpoint that receives updates for your bot. The format is `uri`. The value must match `^https://`. The maximum length is 2,048 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
