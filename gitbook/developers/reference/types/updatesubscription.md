# UpdateSubscription

Cancel the specified buyer's subscription without refunding confirmed payments.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `is_canceled` | `true` | Yes | Cancel future renewals. Re-enabling or extending access is not supported by this operation. |
| `user_id` | string | Yes | Public ID of this subscription's buyer, available to this bot. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
