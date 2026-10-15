# Poll

The current state of a poll or quiz, including options, votes, and closing settings.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `allow_adding_options` | boolean | Yes | This is true when voters can add poll options. |
| `allows_multiple_answers` | boolean | Yes | This is true when a voter can select more than one poll option. |
| `allows_revoting` | boolean | Yes | This is true when a voter can change an existing vote. |
| `close_at` | string | No | This records when the poll closes automatically, in RFC 3339 format. The format is `date-time`. |
| `correct_option_ids` | Array of string | No | These UUIDs identify the correct quiz options. They differ from the zero-based indexes used when creating a quiz. Each item must be unique. |
| `correct_option_ids[]` | string | Per item | The format is `uuid`. |
| `description` | [PollDescription](polldescription.md) | No | This is the description displayed for the object. |
| `explanation` | [PollExplanation](pollexplanation.md) | No | This explains the correct quiz answer. |
| `explanation_media` | [PollMedia](pollmedia.md) | No | This contains media displayed with the quiz explanation. |
| `hide_results_until_closes` | boolean | Yes | This is true when poll results remain hidden until the poll closes. |
| `id` | string | Yes | This UUID identifies the poll. The format is `uuid`. |
| `is_anonymous` | boolean | Yes | This is true when votes are anonymous. Non-anonymous polls may identify voters in poll_answer updates. |
| `is_closed` | boolean | Yes | This is true when the poll is closed to further votes. |
| `media` | [PollMedia](pollmedia.md) | No | This contains attached media or the named media sources for a rich message. |
| `open_period_seconds` | integer | No | The poll's configured open period in seconds, from 5 through 2,628,000 (about 30 days). close_at records the closing time. |
| `options` | Array of [PollOption](polloption.md) | Yes | These are the poll options in display order. The minimum is 1 item. The maximum is 12 items. |
| `question` | [PollQuestion](pollquestion.md) | Yes | This is the question displayed at the top of the poll. |
| `shuffle_options` | boolean | Yes | This is true when poll options are shuffled for voters. |
| `total_voter_count` | integer | Yes | This is the number of users who have voted in the poll. The minimum is 0. |
| `type` | `"regular"` or `"quiz"` | Yes | The poll kind: regular for a vote without a correct answer, or quiz for a poll with correct options. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
