# MenuButton

The menu button configuration for your bot or one private chat.

You receive one of the following forms. Required fields apply within each form.

**When `type` is `"commands"` or `"default"`:**

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `type` | `"commands"` or `"default"` | Yes | Choose the command menu or the inherited menu. |

**When `type` is `"mini_app"`:**

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `text` | string | Yes | The label displayed on this button. The minimum length is 1 character. The maximum length is 64 characters. |
| `type` | `"mini_app"` | Yes | Open a Mini App registered by this bot. |
| `url` | string | Yes | The link opened when the user activates this button or text link. The format is `uri`. The maximum length is 2,048 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
