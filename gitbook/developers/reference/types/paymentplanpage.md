# PaymentPlanPage

One page of this bot's payment plans.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `items` | Array of [PaymentPlan](paymentplan.md) | Yes | Payment plans in the order returned for this bot. The maximum is 100 items. |
| `next_cursor` | string or null | Yes | Pass this value as cursor on the next request. Null marks the end of the list. The value must match `^[!-~]+$`. The minimum length is 1 character. The maximum length is 128 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
