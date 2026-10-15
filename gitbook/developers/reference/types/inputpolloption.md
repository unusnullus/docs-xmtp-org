# InputPollOption

One poll option, with a label and optional media attachment.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `media` | [InputPollOptionMedia](inputpolloptionmedia.md) | No | This contains attached media or the named media sources for a rich message. |
| `text` | string | Yes | The label displayed for this poll option. You can use up to 100 UTF-16 code units after markup is parsed. |
| `text_entities` | Array of [Entity](entity.md) | No | These define explicit formatting for the text. Use these instead of text_format. Omit this field or send an empty array. Nonempty entity lists are not supported for this text. The maximum is 0 items. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
