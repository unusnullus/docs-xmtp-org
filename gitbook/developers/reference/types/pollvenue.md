# PollVenue

A venue returned as poll media, including its location and address.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `type` | `"venue"` | Yes | The content type is venue. |
| `venue` | object | Yes | This contains the venue name, address, coordinates, and optional provider information. |
| `venue.address` | string | Yes | This is the street address or other readable address of the venue. |
| `venue.location` | object | Yes | This contains geographic coordinates and any live location settings. |
| `venue.location.horizontal_accuracy` | number | No | This is the radius of uncertainty around the coordinates, in meters. The minimum is 0. The maximum is 1,500. |
| `venue.location.latitude` | number | Yes | This is the latitude in degrees, from -90 through 90. |
| `venue.location.longitude` | number | Yes | This is the longitude in degrees, from -180 through 180. |
| `venue.provider` | object | No | This identifies the location provider for the venue. |
| `venue.provider.name` | `"foursquare"` or `"google"` or `"other"` | Yes | This is the display name of the user, bot, contact, or provider. |
| `venue.provider.place_id` | string | Yes | This is the venue identifier assigned by the location provider. |
| `venue.provider.place_type` | string | Yes | This is the venue category assigned by the location provider. |
| `venue.title` | string | Yes | This is the title displayed for the object. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
