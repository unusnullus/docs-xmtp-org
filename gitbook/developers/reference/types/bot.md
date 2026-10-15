# Bot

Your bot's identity, effective settings, and feature availability.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `features` | object | Yes | Feature availability keyed by name. private_text, polling, rich_messages, media, and webhooks are available. Each value is available, disabled, or requires_product. requires_product means that access depends on a product entitlement. |
| `features.*` | `"available"` or `"disabled"` or `"requires_product"` | No | The availability of the named feature. The value available means you can use it. The value disabled means it is unavailable. The value requires_product means access depends on a product entitlement. |
| `id` | string | Yes | Your bot's ID, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `is_bot` | `true` | Yes | This is true when this account is a bot. |
| `is_test` | boolean | Yes | This is true when your bot is restricted to its configured testers. |
| `name` | string | Yes | Your bot's current display name. |
| `official_role` | string or null | Yes | This is the official role assigned to the bot, or null for an ordinary bot. |
| `settings` | object | Yes | Your bot's effective boolean settings, keyed by name. The response includes can_join_groups, can_read_all_group_messages, supports_join_request_queries, supports_inline_queries, supports_inline_location, has_main_app, has_topics_enabled, allows_users_to_create_topics, can_connect_to_business, can_manage_bots, supports_guest_queries, can_talk_to_bots, and paid_broadcast_enabled. A true value enables the setting; feature availability still applies. |
| `settings.*` | boolean | No | The effective value of the named setting. A true setting does not grant a capability that features reports as disabled. |
| `username` | string | Yes | Your bot's public handle, without a leading @. Users can use this handle to find and start your bot. |
| `verification_status` | string | Yes | This is the verification status of your bot's account. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
