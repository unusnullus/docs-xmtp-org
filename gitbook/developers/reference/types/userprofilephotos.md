# UserProfilePhotos

The user's current profile photo, returned in up to four sizes.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `items` | Array of [Photo](photo.md) | Yes | These are the items in their returned or requested order. The maximum is 1 item. |
| `items[]` | [Photo](photo.md) | Per item | The maximum is 4 items. |
| `next_cursor` | null | Yes | This is the cursor for another page, or null when there is no further page. Only the current photo is returned, so there is no further page. |
| `total_count` | integer | Yes | This is the total number of items or reactions in this collection. The minimum is 0. The maximum is 1. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
