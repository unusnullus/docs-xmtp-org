# PrivateTopicBotCreate

Create a private topic with a required name and optional icon.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `icon_color` | `7322096` or `16766590` or `13338331` or `9367192` or `16749490` or `16478047` | No | The fixed RGB icon color, chosen from the supported colors. |
| `icon_key` | string | No | An icon key from topics.list_icons. An empty value in an edit removes the custom icon. The value must match `^[!-~]{1,64}$`. |
| `name` | string | Yes | The topic name, from 1 through 128 characters. The value must match `\S`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
