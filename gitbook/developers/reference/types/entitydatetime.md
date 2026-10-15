# EntityDateTime

A UTF-16 text span representing a formatted date and time.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `at` | string | Yes | This records the date and time of the event or displayed date, in RFC 3339 format. |
| `date_time_format` | string | Yes | This is the display format for the date and time entity. |
| `length` | integer | Yes | This is the length of the entity in UTF-16 code units. The minimum is 1. |
| `offset` | integer | Yes | This is the zero-based start of the entity in UTF-16 code units. The minimum is 0. |
| `type` | `"date_time"` | Yes | The formatting entity type is date_time. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
