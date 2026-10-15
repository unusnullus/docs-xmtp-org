# InputFile

One file source for message media: an existing file, a named upload part, or a public URL. Provide exactly one source: an existing file_id or the attach name of a binary part in this request. Where a url field is available, you can instead import a file from a public HTTP or HTTPS URL. Live photo sources do not accept URLs.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `attach` | string | No | This is the name of the binary part in the same multipart request. Use the part name without an attach:// prefix. The minimum length is 1 character. The maximum length is 8,192 characters. |
| `file_id` | string | No | Your bot can use this opaque identifier to send or retrieve the file. The minimum length is 1 character. The maximum length is 8,192 characters. |
| `url` | string | No | A public HTTP or HTTPS URL from which to import the file. The allowed file kind and import size limit depend on the media you send. The minimum length is 1 character. The maximum length is 8,192 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
