# PreCheckoutAnswer

The approval or buyer-visible rejection of one pre-checkout query.

Only the listed fields are allowed. If `ok` is omitted or is `false`, include `error_message`. If `ok` is omitted or is `true`, omit `error_message`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `error_message` | string | No | Plain-text reason shown to the buyer. Required when ok is false; omitted when ok is true. The minimum length is 1 character. The maximum length is 256 characters. |
| `ok` | boolean | Yes | Approve the payment when true; decline it when false. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
