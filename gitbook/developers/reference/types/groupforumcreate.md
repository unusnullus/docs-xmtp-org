# GroupForumCreate

Create a named topic in a group with forums enabled.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `icon_color` | `7322096` or `16766590` or `13338331` or `9367192` or `16749490` or `16478047` | No | The RGB icon color, chosen from the supported topic colors. |
| `icon_key` | `"chat"` or `"book"` or `"idea"` or `"code"` or `"heart"` or `"star"` | No | An optional icon key from the supported topic icon catalogue. |
| `name` | string | Yes | The forum topic title, from 1 through 128 characters. The value must match `\S`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
