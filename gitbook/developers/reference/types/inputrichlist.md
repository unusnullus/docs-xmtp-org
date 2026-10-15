# InputRichList

An ordered or unordered list of rich content items. Use this node when sending rich content.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `items` | Array of object | Yes | These are the items in their returned or requested order. The minimum is 1 item. |
| `items[]` | object | Per item | Only the listed fields are allowed. |
| `items[].blocks` | Array of [InputRichBlock](inputrichblock.md) | Yes | These are the rich content blocks in display order. |
| `items[].has_checkbox` | boolean | No | This is true when this list item displays a checkbox. |
| `items[].is_checked` | boolean | No | This is true when the list item's checkbox is checked. |
| `items[].label` | [InputRichText](inputrichtext.md) | No | This is the formatted label for the list item. |
| `items[].label_type` | `"a"` or `"A"` or `"i"` or `"I"` or `"1"` | No | This selects the numbering style for an ordered list or list item. |
| `items[].value` | integer | No | This is the dice result or numbering value for the list item. The minimum is 0. The maximum is 4,294,967,295. |
| `label_type` | `"a"` or `"A"` or `"i"` or `"I"` or `"1"` | No | This selects the numbering style for an ordered list or list item. |
| `ordered` | boolean | No | This is true when the list uses ordered numbering. |
| `reversed` | boolean | No | This is true when an ordered list counts down. |
| `start` | integer | No | This is the starting number for an ordered list. The minimum is 0. The maximum is 4,294,967,295. |
| `type` | `"list"` | Yes | The rich content node type is list. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
