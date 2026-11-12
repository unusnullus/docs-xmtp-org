# TopicCreatedService

The accepted service message that creates and identifies a topic.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `icon_color` | `7322096` or `16766590` or `13338331` or `9367192` or `16749490` or `16478047` | Yes | The fixed RGB icon color, chosen from the supported colors. |
| `icon_key` | `"chat"` or `"book"` or `"idea"` or `"code"` or `"heart"` or `"star"` | No | An icon key from topics.list_icons. An empty value in an edit removes the custom icon. |
| `is_name_implicit` | boolean | Yes | True when a default name was supplied because the user left the name empty. |
| `name` | string | Yes | The topic name, from 1 through 128 characters. |
| `type` | `"topic_created"` | Yes | The service event type is topic_created. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
