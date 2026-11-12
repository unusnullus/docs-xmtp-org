# InlineResultLocation

An inline location result with an ID chosen by your bot.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `content` | [InlineMessageContent](inlinemessagecontent.md) | No | This contains the message content. The type field selects its variant. |
| `horizontal_accuracy` | number | No | This is the radius of uncertainty around the coordinates, in meters. |
| `id` | string | Yes | The identifier of this object. The minimum length is 1 character. The maximum length is 64 characters. |
| `latitude` | number | Yes | This is the latitude in degrees, from -90 through 90. |
| `live` | object | No | This defines live location sharing settings for the coordinates. |
| `longitude` | number | Yes | This is the longitude in degrees, from -180 through 180. |
| `reply_markup` | [InputInlineKeyboard](inputinlinekeyboard.md) | No | This defines the keyboard attached to the message. The type field selects its variant. |
| `thumbnail` | object | No | This contains the thumbnail file or source associated with the media. |
| `title` | string | Yes | This is the title displayed for the object. |
| `type` | `"location"` | Yes | The content type is location. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
