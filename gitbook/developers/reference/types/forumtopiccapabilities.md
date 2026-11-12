# ForumTopicCapabilities

Operations currently available to your bot for this forum topic.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `close` | boolean | Yes | Whether your bot can close this topic. |
| `delete` | boolean | Yes | Whether topic deletion is available to your bot. |
| `edit` | boolean | Yes | Whether your bot can edit this topic's metadata. |
| `hide` | boolean | Yes | Whether your bot can hide General. |
| `reopen` | boolean | Yes | Whether your bot can reopen this topic. |
| `unhide` | boolean | Yes | Whether your bot can unhide General. |
| `unpin_all` | boolean | Yes | Whether your bot can remove every pin in this topic. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
