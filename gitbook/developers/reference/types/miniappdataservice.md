# MiniAppDataService

A service event carrying page data and the text of its saved reply keyboard button.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `button_text` | string | Yes | Text of the saved reply keyboard button that opened the app. The minimum length is 1 character. The maximum length is 64 characters. |
| `data` | string | Yes | Untrusted UTF-8 data supplied by the page. Validate it before use. The maximum length is 4,096 characters. The limit is 4,096 UTF-8 bytes. |
| `type` | `"mini_app_data"` | Yes | Data submitted by a Mini App opened through a reply keyboard button. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
