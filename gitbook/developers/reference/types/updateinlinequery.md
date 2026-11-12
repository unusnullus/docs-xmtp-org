# UpdateInlineQuery

An inline query update containing the requesting user, query text and answer deadline.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `created_at` | string | Yes | When the inline query update was created, in RFC 3339 format. The format is `date-time`. |
| `inline_query` | [InlineQuery](inlinequery.md) | Yes | The inline query to answer within its original deadline. |
| `type` | `"inline_query"` | Yes | The update type is inline_query. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
