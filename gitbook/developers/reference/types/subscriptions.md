# Subscriptions

One cursor page of subscriptions visible to this bot.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `items` | Array of [Subscription](subscription.md) | Yes | Subscriptions included in this page. |
| `next_cursor` | string or null | Yes | Opaque cursor for the next page, or null when pagination is complete. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
