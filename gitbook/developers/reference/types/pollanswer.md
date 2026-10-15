# PollAnswer

A user's current selection of poll option IDs.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `option_ids` | Array of string | Yes | These are the IDs of the poll options selected by the voter. The maximum is 12 items. Each item must be unique. |
| `option_ids[]` | string | Per item | The format is `uuid`. |
| `poll_id` | string | Yes | This is the identifier of the poll. The format is `uuid`. |
| `user` | [User](user.md) | Yes | This identifies the user associated with the event, mention, or private chat. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
