# RichButtonDateTime

A formatted date and time within a rich button label.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `at` | string | Yes | This records the date and time of the event or displayed date, in RFC 3339 format. The format is `date-time`. |
| `format` | string | Yes | The date and time display format. The flag r displays relative time; other supported flags select day and time components. The value must match `^(r\|w?[dD]?[tT]?)$`. |
| `text` | string | Yes | The label displayed on this button. You can use up to 64 UTF-16 code units after markup is parsed. |
| `type` | `"date_time"` | Yes | The button action type is date_time. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
