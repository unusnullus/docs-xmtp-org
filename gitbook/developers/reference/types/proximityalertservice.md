# ProximityAlertService

A service event reporting that a live location entered another user's proximity radius.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `distance` | integer | Yes | This is the distance between the two users, in meters. Great-circle distance rounded to meters. The minimum is 0. |
| `traveler` | [User](user.md) | Yes | This identifies the user whose live location entered the proximity radius. |
| `type` | `"proximity_alert_triggered"` | Yes | The service event type is proximity_alert_triggered. |
| `watcher` | [User](user.md) | Yes | This identifies the user whose proximity radius was entered. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
