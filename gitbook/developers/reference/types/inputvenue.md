# InputVenue

The coordinates, name, address, and optional provider details to send as a venue card.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `address` | string | Yes | The venue address, from 1 through 1,024 UTF-16 code units. It must contain non-whitespace text. |
| `latitude` | number | Yes | This is the latitude in degrees, from -90 through 90. |
| `longitude` | number | Yes | This is the longitude in degrees, from -180 through 180. |
| `provider` | [VenueProvider](venueprovider.md) | No | This identifies the location provider for the venue. |
| `title` | string | Yes | The venue name, from 1 through 256 UTF-16 code units. It must contain non-whitespace text. |
| `type` | `"venue"` | Yes | The content type is venue. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
