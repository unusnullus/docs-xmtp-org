# AnswerSavedInlineQuery

One personal uncached answer within the saved query's original deadline. Unsupported cache and pagination options are rejected; only the Mini App header button is supported.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `button` | [SavedInlineAppButton](savedinlineappbutton.md) | No | Optional Mini App header button shown above the results, including when the results array is empty. |
| `cache_seconds` | `0` | No | Must be zero. Saved inline answers are not cached and retain the query's original deadline. The default is `0`. |
| `is_personal` | `true` | No | Must be true. The answer is personal to the user who issued this query. The default is `true`. |
| `next_offset` | `""` | No | Must be empty. Saved inline answers do not support pagination. The default is `""`. |
| `results` | Array of [InlineResult](inlineresult.md) | Yes | Up to 50 supported inline results. An empty array can still show the Mini App header button. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
