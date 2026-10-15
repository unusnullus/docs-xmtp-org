# PollDescription

The additional poll description and its formatting entities.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `entities` | Array of [Entity](entity.md) | Yes | These define formatting with offsets and lengths measured in UTF-16 code units. In requests, use these instead of format. |
| `text` | string | Yes | The additional description displayed with the poll question. You can use up to 1,024 UTF-16 code units after markup is parsed. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
