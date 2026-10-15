# EntityHashtag

A UTF-16 text span representing a hashtag.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_username` | string | No | This is the handle of the chat associated with the entity. The maximum length is 64 characters. |
| `length` | integer | Yes | This is the length of the entity in UTF-16 code units. The minimum is 1. |
| `offset` | integer | Yes | This is the zero-based start of the entity in UTF-16 code units. The minimum is 0. |
| `type` | `"hashtag"` | Yes | The formatting entity type is hashtag. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
