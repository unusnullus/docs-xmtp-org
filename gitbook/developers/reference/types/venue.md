# Venue

The venue name, address, coordinates, and optional provider information returned in a message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `address` | string | Yes | The venue address, from 1 through 1,024 UTF-16 code units. It must contain non-whitespace text. |
| `location` | object | Yes | This contains geographic coordinates and any live location settings. |
| `location.latitude` | number | Yes | This is the latitude in degrees, from -90 through 90. |
| `location.longitude` | number | Yes | This is the longitude in degrees, from -180 through 180. |
| `provider` | [VenueProvider](venueprovider.md) | No | This identifies the location provider for the venue. |
| `title` | string | Yes | The venue name, from 1 through 256 UTF-16 code units. It must contain non-whitespace text. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
