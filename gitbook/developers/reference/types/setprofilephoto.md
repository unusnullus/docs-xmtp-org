# SetProfilePhoto

The uploaded photo or animation to use as your bot's profile photo. Send one new file part in a multipart request. For a static photo, use JPEG, PNG, or WebP with a maximum size of 10 MiB.

Supply exactly one of the following forms. Required fields apply within each form.

**When `type` is `"static"`:**

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `photo` | object | Yes | This contains the photo sizes or photo source for the object. Only the listed fields are allowed. |
| `photo.attach` | string | Yes | This is the name of the binary part in the same multipart request. Use the part name without an attach:// prefix. The value must match `^[A-Za-z0-9_-]{1,64}$`. |
| `type` | `"static"` | Yes | The content type is static. |

**When `type` is `"animated"`:**

Upload a new square MP4 video encoded with H.264, at most 10 seconds long and 10 MiB in size. The selected frame must be within the decoded duration. Existing file IDs and URLs are not accepted. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `animation` | object | Yes | This contains the animation file or source for the content. Only the listed fields are allowed. |
| `animation.attach` | string | Yes | This is the name of the binary part in the same multipart request. Use the part name without an attach:// prefix. The value must match `^[A-Za-z0-9_-]{1,64}$`. |
| `main_frame_seconds` | number | No | This is the position of the frame used as the static profile photo, in seconds. The minimum is 0. The value must be less than 10. |
| `type` | `"animated"` | Yes | The content type is animated. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
