# InputPollVenue

A static venue to attach to a poll, including coordinates, name, address, and optional provider details.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `address` | string | Yes | The nonempty address of the venue attached to the poll. It must contain non-whitespace text. |
| `latitude` | number | Yes | This is the latitude in degrees, from -90 through 90. |
| `longitude` | number | Yes | This is the longitude in degrees, from -180 through 180. |
| `provider` | object | No | This identifies the location provider for the venue. Only the listed fields are allowed. |
| `provider.name` | `"foursquare"` or `"google"` or `"other"` | Yes | This is the display name of the user, bot, contact, or provider. |
| `provider.place_id` | string | Yes | This is the venue identifier assigned by the location provider. The minimum length is 1 character. |
| `provider.place_type` | string | Yes | This is the venue category assigned by the location provider. The minimum length is 1 character. |
| `title` | string | Yes | The nonempty name of the venue attached to the poll. It must contain non-whitespace text. |
| `type` | `"venue"` | Yes | The content type is venue. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
