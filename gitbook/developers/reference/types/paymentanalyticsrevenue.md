# PaymentAnalyticsRevenue

Exact payment revenue in one configured asset.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `amount` | string | Yes | Exact nonnegative revenue in base units, aggregated by the payment service. The value must match `^(0\|[1-9][0-9]*)$`. The maximum length is 256 characters. |
| `asset` | string | Yes | The configured asset identifier for this revenue aggregate. The value must match `^[!-~]+$`. The minimum length is 1 character. The maximum length is 256 characters. |
| `decimals` | integer | Yes | Base-unit decimals from the asset catalog. The minimum is 0. The maximum is 255. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
