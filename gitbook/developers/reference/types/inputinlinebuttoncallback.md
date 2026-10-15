# InputInlineButtonCallback

A button that sends a callback query to your bot.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `data` | string | Yes | Your bot receives this callback data when the user presses the button. You can use up to 64 UTF-8 bytes. The minimum length is 1 character. |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `text` | string | Yes | The label displayed on this button. You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `type` | `"callback"` | Yes | The button action type is callback. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
