# UpdateCallbackQuery

An update carrying an inline button press.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `callback_query` | [CallbackQuery](callbackquery.md) | Yes | You receive this callback when a user presses an inline button. |
| `created_at` | string | Yes | This records when the button press update was created, in RFC 3339 format. The format is `date-time`. |
| `type` | `"callback_query"` | Yes | The update type is callback_query. |
| `update_id` | string | Yes | This identifies the update and is encoded as a decimal string. For polling, pass the last processed ID plus one as the next offset. The value must match `^[1-9][0-9]*$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
