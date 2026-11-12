# WriteAccessAllowedService

A service event identifying the explicit source of permission to write to the user.

The response includes exactly one of `from_request`, `mini_app_name`, or `from_attachment_menu`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `from_attachment_menu` | `true` | No | The user granted permission while installing the attachment or side menu. |
| `from_request` | `true` | No | The user accepted requestWriteAccess. |
| `mini_app_name` | string | No | The registered short name of the app whose opening granted permission. The value must match `^[a-z][a-z0-9_]{2,31}$`. |
| `type` | `"write_access_allowed"` | Yes | An explicit permission for this bot to write to the user. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
