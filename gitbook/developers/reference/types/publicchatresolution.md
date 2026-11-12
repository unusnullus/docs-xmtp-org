# PublicChatResolution

The canonical chat ID and kind of an active public group or channel. This metadata grants no membership or content access.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_id` | string | Yes | The public group or channel's canonical directory ID in [2^40, 2^62), encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |
| `kind` | `"group"` or `"channel"` | Yes | The resolved public chat kind: group or channel. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
