# TopicEditedService

The accepted service message describing a changed topic name or icon.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `icon_key` | string | No | An icon key from topics.list_icons. An empty value in an edit removes the custom icon. |
| `name` | string | No | The topic name, from 1 through 128 characters. |
| `type` | `"topic_edited"` | Yes | The service event type is topic_edited. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
