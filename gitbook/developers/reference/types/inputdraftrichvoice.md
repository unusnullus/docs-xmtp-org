# InputDraftRichVoice

A voice message block with optional caption text. Use this node in a temporary rich draft.

Only the listed fields are allowed. Include exactly one of `file` or `media_id`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `caption` | [InputDraftRichCaption](inputdraftrichcaption.md) | No | This is the caption displayed with the media. |
| `file` | [InputDraftFile](inputdraftfile.md) | No | An existing file available to your bot. Draft media can reference file_id only; uploads and URL imports are rejected. |
| `media_id` | string | No | Use the ID of a source in this rich message's media dictionary instead of file. |
| `type` | `"voice"` | Yes | The rich content node type is voice. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
