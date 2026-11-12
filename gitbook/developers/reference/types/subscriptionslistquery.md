# SubscriptionsListQuery

Optional filters applied before cursor pagination of this bot's subscriptions.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `cursor` | string | No | The last next_cursor, or an empty value for the first page. The value must match `^[A-Za-z0-9_:-]*$`. The maximum length is 128 characters. |
| `limit` | integer | No | Maximum subscriptions per page. The minimum is 1. The maximum is 100. The default is `20`. |
| `plan_id` | string | No | Filter by this exact payment plan ID. The minimum length is 1 character. The maximum length is 128 characters. |
| `state` | `"active"` or `"past_due"` or `"canceled"` or `"expired"` or `"refunded"` or `"ended"` | No | Filter by the current subscription state. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
