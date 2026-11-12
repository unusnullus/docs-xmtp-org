# PrivateTopicIcons

Versioned icon catalogue with supported colors and SVG vectors.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `colors` | Array of integer | Yes | Supported icon colors as RGB integers. The minimum is 6 items. The maximum is 6 items. |
| `icons` | Array of object | Yes | The supported icon keys and their SVG vectors. |
| `icons[].key` | `"chat"` or `"book"` or `"idea"` or `"code"` or `"heart"` or `"star"` | Yes | The icon key accepted when creating or editing a topic. |
| `icons[].svg` | string | Yes | SVG vector supplied by the server. Render it using the topic color. |
| `version` | `"1"` | Yes | Catalogue version. Cache icons by this value. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
