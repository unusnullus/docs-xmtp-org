# InputRichInlineUrl

An inline text link to a URL. Use this node when sending rich content.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `text` | [InputRichText](inputrichtext.md) | Yes | The text displayed for the URL link. |
| `type` | `"url"` | Yes | The rich content node type is url. |
| `url` | string | Yes | The link opened when the user activates this button or text link. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
