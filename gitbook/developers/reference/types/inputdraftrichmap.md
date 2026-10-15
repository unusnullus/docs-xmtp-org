# InputDraftRichMap

A map block showing coordinates within rich content. Use this node in a temporary rich draft. The sum of the layout width and height must be at most 10,000 pixels. When both dimensions are nonzero, their aspect ratio must be at most 20:1. The response contains the map coordinates and layout.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | [InputDraftRichCaption](inputdraftrichcaption.md) | No | This is the caption displayed with the media. |
| `height` | integer | No | This is the height of the media or layout, in pixels. The minimum is 0. The maximum is 10,000. The default is `0`. |
| `location` | object | Yes | This contains geographic coordinates and any live location settings. Only the listed fields are allowed. |
| `location.latitude` | number | Yes | This is the latitude in degrees, from -90 through 90. |
| `location.longitude` | number | Yes | This is the longitude in degrees, from -180 through 180. |
| `type` | `"map"` | Yes | The rich content node type is map. |
| `width` | integer | No | This is the width of the media or layout, in pixels. The minimum is 0. The maximum is 10,000. The default is `0`. |
| `zoom` | integer | No | This is the map zoom level, from 0 through 24. The default is `12`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
