# PaymentAnalyticsBucket

One day of payment and manual-subscription aggregates.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `active_at_end` | integer | Yes | Active subscriptions at the end of this day. The minimum is 0. The maximum is 9,007,199,254,740,991. |
| `avg_tenure_days` | string or null | Yes | Average subscription tenure in decimal days, or null when unavailable. The value must match `^(0\|[1-9][0-9]{0,17})(\.[0-9]{1,9})?$`. |
| `churned` | integer | Yes | Subscriptions that churned during this day. The minimum is 0. The maximum is 9,007,199,254,740,991. |
| `date` | string | Yes | UTC calendar date of this aggregate. The format is `date`. |
| `failed_renewals` | integer | Yes | Failed renewal attempts during this day. The minimum is 0. The maximum is 9,007,199,254,740,991. |
| `mrr_usd_cents` | string or null | Yes | Monthly recurring revenue in USD cents supplied by the payment service, or null when unavailable. The value must match `^(0\|[1-9][0-9]*)$`. The maximum length is 256 characters. |
| `new` | integer | Yes | New subscriptions during this day. The minimum is 0. The maximum is 9,007,199,254,740,991. |
| `past_due` | integer | Yes | Subscriptions entering past-due state during this day. The minimum is 0. The maximum is 9,007,199,254,740,991. |
| `reactivated` | integer | Yes | Subscriptions reactivated during this day. The minimum is 0. The maximum is 9,007,199,254,740,991. |
| `renewals` | integer | Yes | Confirmed manual subscription renewals during this day. The minimum is 0. The maximum is 9,007,199,254,740,991. |
| `revenue` | Array of [PaymentAnalyticsRevenue](paymentanalyticsrevenue.md) | Yes | Revenue per asset. Different assets are never added together. The maximum is 128 items. |
| `revenue_usd_cents` | string or null | Yes | Stablecoin revenue in USD cents supplied by the payment service, or null when unavailable. The value must match `^(0\|[1-9][0-9]*)$`. The maximum length is 256 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
