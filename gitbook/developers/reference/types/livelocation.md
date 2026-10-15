# LiveLocation

The duration, expiration, and movement settings of live location sharing.

If `indefinite` is present, `expires_at` has the type null. If `period_seconds` is present, `expires_at` has the type string.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `expires_at` | string or null | Yes | This is the expiration date and time in RFC 3339 format, or null when the live location has no fixed expiration. The format is `date-time`. |
| `heading` | integer | No | This is the direction of travel in degrees, from 1 through 360. |
| `indefinite` | `true` | No | This is true when live location sharing has no fixed expiration. Use this instead of period_seconds. |
| `period_seconds` | integer | No | The total sharing duration in seconds from the original sharing start. It begins between 60 and 86,400 seconds (one day) and may grow through edits. Each finite extension adds at most 86,400 seconds, and its expiration can be at most 90 days after that edit. Use expires_at to determine when sharing ends. |
| `proximity_alert_radius` | integer | No | This is the distance in meters at which to trigger a proximity alert. The minimum is 1. The maximum is 100,000. |

You receive one of the following forms. Required fields apply within each form.

- The response includes `period_seconds`. The response omits `indefinite`.

- The response includes `indefinite`. The response omits `period_seconds`.

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
