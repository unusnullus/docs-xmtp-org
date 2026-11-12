# UploadBusinessEndpointPrekeys

Public Business endpoint data for UploadBusinessEndpointPrekeys. No runtime private key is accepted or returned.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `prekeys` | Array of [BusinessEndpointPrekey](businessendpointprekey.md) | Yes | Public one-time prekeys to add. Consumed IDs cannot be reused for this endpoint identity. The minimum is 1 item. The maximum is 100 items. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
