# InputInlineButtonMiniApp

An inline button that opens a Mini App on a registered origin when Mini Apps are enabled.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `style` | `"primary"` or `"success"` or `"danger"` | No | This selects the visual style of the button. |
| `text` | string | Yes | You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `type` | `"mini_app"` | Yes | The button action type is mini_app. |
| `url` | string | Yes | A Mini App URL on a registered, unblocked origin of this bot. Available only when Mini Apps are enabled. The format is `uri`. The maximum length is 2,048 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
