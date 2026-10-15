# InlineButtonUrl

A button that opens a link.

The response includes exactly one of `url` or `user_id`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `text` | string | Yes | The label displayed on this button. You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `type` | `"url"` | Yes | The button action type is url. |
| `url` | string | No | The link opened when the user activates this button or text link. The format is `uri`. |
| `user_id` | string | No | This is the user ID, encoded as a decimal string. A user ID differs from a private chat ID. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
