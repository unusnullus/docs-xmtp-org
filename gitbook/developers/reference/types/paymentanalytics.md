# PaymentAnalytics

Daily payment aggregates for the requested bot and date range.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `buckets` | Array of [PaymentAnalyticsBucket](paymentanalyticsbucket.md) | Yes | Daily aggregates in ascending date order. Missing dates are not synthesized. The maximum is 366 items. |
| `from` | string | Yes | Inclusive UTC calendar date requested for this report. The format is `date`. |
| `granularity` | `"day"` | Yes | Reports contain UTC calendar-day aggregates. |
| `plan_id` | string or null | Yes | The requested plan filter, or null for all plans owned by this bot. The value must match `^[A-Za-z0-9_:-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `to` | string | Yes | Exclusive UTC calendar date requested for this report. The format is `date`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
