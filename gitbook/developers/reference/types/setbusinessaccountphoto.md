# SetBusinessAccountPhoto

The new photo for the connected account. Supply the same body and Idempotency-Key when retrying the original request.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `is_public` | `false` | No | Whether to set the public fallback photo. Only false is supported. The default is `false`. |
| `photo` | object | Yes | This contains the photo sizes or photo source for the object. Only the listed fields are allowed. |
| `photo.photo` | object | Yes | This contains the photo sizes or photo source for the object. Only the listed fields are allowed. |
| `photo.photo.file_id` | string | Yes | A new static JPEG uploaded by this same bot through files.upload; copied profile, URL, attachment and foreign-bot references are rejected. The minimum length is 1 character. The maximum length is 8,192 characters. |
| `photo.type` | `"static"` | Yes | The content type is static. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
