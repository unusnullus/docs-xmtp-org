# RichInlineDateTime

A formatted date and time within inline rich text.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `at` | string | Yes | This records the date and time of the event or displayed date, in RFC 3339 format. |
| `format` | string | Yes | The date and time display format. The flag r displays relative time; other supported flags select day and time components. |
| `text` | [RichText](richtext.md) | Yes | The text label associated with the formatted date and time. |
| `type` | `"date_time"` | Yes | The rich content node type is date_time. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
