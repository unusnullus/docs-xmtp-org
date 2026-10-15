# EditLiveLocation

Changes to an active live location authored by your bot. Coordinates are required, and omitted settings keep their values. The sharing period starts at the original send time. You can extend it by up to 86,400 seconds (one day) per edit, with an expiration no later than 90 days after the edit. You can change the location once every 5 seconds per message.

Only the listed fields are allowed. Do not supply `period_seconds` and `indefinite` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `heading` | integer | No | This is the direction of travel in degrees, from 1 through 360. |
| `horizontal_accuracy` | number | No | This is the radius of uncertainty around the coordinates, in meters. The minimum is 0. The maximum is 1,500. |
| `indefinite` | `true` | No | This is true when live location sharing has no fixed expiration. Use this instead of period_seconds. |
| `latitude` | number | Yes | This is the latitude in degrees, from -90 through 90. |
| `longitude` | number | Yes | This is the longitude in degrees, from -180 through 180. |
| `period_seconds` | integer | No | The total sharing duration in seconds, measured from the original sharing start. The minimum is 60. For a finite location, you can increase the previous duration by at most 86,400 seconds (one day) per edit. The resulting expiration must be in the future and at most 7,776,000 seconds (90 days) after this edit. Omit this field to keep the duration, or use indefinite instead. |
| `proximity_alert_radius` | integer | No | This is the distance in meters at which to trigger a proximity alert. The minimum is 1. The maximum is 100,000. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
