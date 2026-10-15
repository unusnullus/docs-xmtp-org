# PollOption

A poll option with a stable UUID, its label, and vote count.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `added_at` | string | No | This records when the option was added, in RFC 3339 format. The format is `date-time`. |
| `added_by_user` | [User](user.md) | No | This identifies the user who added this poll option. |
| `id` | string | Yes | This UUID identifies the poll option. The format is `uuid`. |
| `media` | [PollOptionMedia](polloptionmedia.md) | No | This contains attached media or the named media sources for a rich message. |
| `text` | [PollOptionText](polloptiontext.md) | Yes | The formatted label displayed for this poll option. |
| `voter_count` | integer | Yes | This is the number of votes for this poll option. The minimum is 0. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
