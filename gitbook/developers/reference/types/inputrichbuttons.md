# InputRichButtons

A row of buttons embedded in rich content. Use this node when sending rich content.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `align` | `"left"` or `"center"` or `"right"` | No | This controls the horizontal alignment of a cell or button row. The default is `"left"`. |
| `buttons` | Array of [InputRichButton](inputrichbutton.md) | Yes | These are the buttons in this row, in display order. The minimum is 1 item. The maximum is 8 items. |
| `type` | `"buttons"` | Yes | The button action type is buttons. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
