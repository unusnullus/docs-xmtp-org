# PrivateTopicHumanCreate

Create a private topic. An omitted or empty name uses a localized default.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `icon_color` | `7322096` or `16766590` or `13338331` or `9367192` or `16749490` or `16478047` | No | The fixed RGB icon color, chosen from the supported colors. |
| `icon_key` | string | No | An icon key from topics.list_icons. An empty value in an edit removes the custom icon. The value must match `^[!-~]{1,64}$`. |
| `name` | `""` or string | No | Optional name. Omit it or send an empty string to use a localized default. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
