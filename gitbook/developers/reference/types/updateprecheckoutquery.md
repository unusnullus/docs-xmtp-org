# UpdatePreCheckoutQuery

An urgent pre-checkout query that expires at its original deadline.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | This records when the pre-checkout query update was created, in RFC 3339 format. The format is `date-time`. |
| `expires_at` | string | No | The original pre-checkout deadline in RFC 3339 format. Answer this query before this time; retries do not extend it. The format is `date-time`. |
| `pre_checkout_query` | [PreCheckoutQuery](precheckoutquery.md) | Yes | The merchant approval query with its original ten-second deadline. |
| `type` | `"pre_checkout_query"` | Yes | The update type is pre_checkout_query. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
