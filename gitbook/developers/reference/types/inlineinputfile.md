# InlineInputFile

An existing bot file or an HTTPS source to import when selected.

Supply exactly one of the following forms. Required fields apply within each form.

- An existing file available to your bot: [InlineCachedFile](inlinecachedfile.md).

**When `url` has the type string:**

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `url` | string | Yes | The HTTPS URL used for this result. The format is `uri`. The value must match `^https://`. The maximum length is 2,048 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
