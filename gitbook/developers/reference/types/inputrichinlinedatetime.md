# InputRichInlineDateTime

A formatted date and time within inline rich text. Use this node when sending rich content.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `at` | string | Yes | This records the date and time of the event or displayed date, in RFC 3339 format. |
| `format` | string | Yes | Choose how to display the date and time. Use r for relative time or the supported day and time format flags. |
| `text` | [InputRichText](inputrichtext.md) | Yes | The text label associated with the formatted date and time. |
| `type` | `"date_time"` | Yes | The rich content node type is date_time. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
