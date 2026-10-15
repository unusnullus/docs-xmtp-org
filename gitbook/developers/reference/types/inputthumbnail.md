# InputThumbnail

A separate JPEG upload used as a custom media thumbnail. Upload a separate JPEG part smaller than 200,000 bytes (at most 199,999 bytes) and no larger than 320 by 320 pixels. You cannot use a URL, an existing file ID, or a part reused by another source.

Supply exactly one of `attach`. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `attach` | string | No | This is the name of the binary part in the same multipart request. Use the part name without an attach:// prefix. The minimum length is 1 character. The maximum length is 8,192 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
