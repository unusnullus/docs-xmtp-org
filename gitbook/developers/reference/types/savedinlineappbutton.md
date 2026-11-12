# SavedInlineAppButton

A Mini App header supplied by the authenticated bot. Its label and currently registered HTTPS URL remain available when the results array is empty.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `mini_app` | object | Yes | The Mini App to open. Its HTTPS URL must be currently registered to the authenticated bot. Only the listed fields are allowed. |
| `mini_app.url` | string | Yes | The link opened when the user activates this button or text link. The format is `uri`. The value must match `^https://`. The maximum length is 2,048 characters. |
| `text` | string | Yes | The label displayed on this button. The minimum length is 1 character. The maximum length is 256 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
