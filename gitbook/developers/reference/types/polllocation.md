# PollLocation

A static location returned as poll media.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `location` | object | Yes | This contains geographic coordinates and any live location settings. |
| `location.horizontal_accuracy` | number | No | This is the radius of uncertainty around the coordinates, in meters. The minimum is 0. The maximum is 1,500. |
| `location.latitude` | number | Yes | This is the latitude in degrees, from -90 through 90. |
| `location.longitude` | number | Yes | This is the longitude in degrees, from -180 through 180. |
| `type` | `"location"` | Yes | The content type is location. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
