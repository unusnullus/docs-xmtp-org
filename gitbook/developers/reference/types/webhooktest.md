# WebhookTest

The HTTP status and elapsed time from a test of your configured webhook endpoint.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `error` | `"circuit_open"` or `"redirect_not_permitted"` or `"delivery_failed"` | No | This describes a failed request or webhook test. |
| `latency_ms` | integer | Yes | This is the time taken by the webhook test, in milliseconds. The minimum is 0. |
| `status_code` | integer | Yes | This is the HTTP status returned by the webhook test. Zero means no HTTP response was received. The minimum is 0. The maximum is 599. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
