# PaymentOneTimePlan

A payment plan purchased once.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `grace_seconds` | `0` | Yes | Creator-configured grace duration in seconds, from 0 to 14 days. Zero for a one-time plan. |
| `kind` | `"one_time"` | Yes | Whether the plan is purchased once or renewed manually for each period. |
| `period_seconds` | `0` | Yes | Duration of each recurring paid period, in seconds. Zero for a one-time plan. |
| `plan_id` | string | Yes | The opaque identifier of a payment plan owned by this bot. The value must match `^[A-Za-z0-9_:-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `token` | [PaymentTokenAmount](paymenttokenamount.md) | Yes | The exact price in the configured asset and its base-unit decimals. |
| `version` | integer | Yes | The version of these plan terms assigned by the payment service. The minimum is 1. The maximum is 4,294,967,295. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
