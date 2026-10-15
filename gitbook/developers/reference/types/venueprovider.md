# VenueProvider

The map provider and its place identifier and category.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `name` | `"foursquare"` or `"google"` or `"other"` | Yes | This is the display name of the user, bot, contact, or provider. |
| `place_id` | string | Yes | The provider's place identifier, from 1 through 256 UTF-16 code units, with non-whitespace text. |
| `place_type` | string | Yes | The provider's place category, from 1 through 256 UTF-16 code units, with non-whitespace text. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
