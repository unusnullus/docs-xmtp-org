# SetManagedBotAccessSettings

The desired access settings. The owner always has access and does not need to be listed.

Only the listed fields are allowed. If `is_access_restricted` is omitted or is `true`, Each item must be unique.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `added_user_ids` | Array of string | No | Public decimal human user IDs. Omission means an empty list when restricted. Values are ignored when unrestricted. The maximum is 10 items. The default is `[]`. |
| `is_access_restricted` | boolean | Yes | Whether to restrict the bot to the owner and listed people. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
