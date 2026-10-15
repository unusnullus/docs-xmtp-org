# Location

The coordinates and optional live sharing state returned in a location message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `horizontal_accuracy` | number | No | This is the radius of uncertainty around the coordinates, in meters. The minimum is 0. The maximum is 1,500. |
| `latitude` | number | Yes | This is the latitude in degrees, from -90 through 90. |
| `live` | [LiveLocation](livelocation.md) | No | This defines live location sharing settings for the coordinates. |
| `longitude` | number | Yes | This is the longitude in degrees, from -180 through 180. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
