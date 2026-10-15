# InputLiveLocation

The duration and optional movement settings for new live location sharing.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `heading` | integer | No | This is the direction of travel in degrees, from 1 through 360. |
| `indefinite` | `true` | No | This is true when live location sharing has no fixed expiration. Use this instead of period_seconds. |
| `period_seconds` | integer | No | The initial sharing duration, from 60 through 86,400 seconds (one day), measured from the send time. Use this instead of indefinite. |
| `proximity_alert_radius` | integer | No | This is the distance in meters at which to trigger a proximity alert. The minimum is 1. The maximum is 100,000. |

Supply exactly one of the following forms. Required fields apply within each form.

- Include `period_seconds`. Omit `indefinite`.

- Include `indefinite`. Omit `period_seconds`.

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
