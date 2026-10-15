# SetDraft

The text or rich content and stop controls for a temporary draft. Use drafts in private chats to show a response while you generate it. A draft lasts for 30 s and stays out of message history. You can use a thinking block in a rich draft, and media must reference existing file IDs. You can update drafts up to 5 times per second per chat. Reusing a stopped draft ID returns HTTP 409 with code version_conflict and reason draft_retired. Sending a final message retires the draft. Deleting the final message does not prevent you from creating a new draft.

Only the listed fields are allowed. Do not supply `format` and `entities` together.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `can_stop` | boolean | No | This is true when the user can stop generation of this draft. The default is `false`. |
| `entities` | Array of [InputEntity](inputentity.md) | No | These define formatting with offsets and lengths measured in UTF-16 code units. In requests, use these instead of format. |
| `format` | `"plain"` or `"html"` or `"markdown"` | No | Choose the markup format used to parse text. Use this instead of entities. |
| `keep_on_stop` | boolean | No | This is true when the current draft remains visible when the user stops generation. The default is `false`. |
| `rich` | [InputDraftRich](inputdraftrich.md) | No | This contains structured rich message content. |
| `text` | string | No | The temporary draft text shown while your bot generates a response. You can use 0 to 4,096 UTF-16 code units after markup is parsed. An empty string displays the thinking indicator. |

Supply exactly one of the following forms. Required fields apply within each form.

- Include `text`. Omit `rich`.

- Include `rich`. Omit `text`, `format`, and `entities`.

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
