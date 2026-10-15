# InputPoll

The question, options, and voting rules for a new poll or quiz.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `allow_adding_options` | boolean | No | This is true when voters can add poll options. You can enable this only for a regular, non-anonymous poll. The default is `false`. |
| `allows_multiple_answers` | boolean | No | This is true when a voter can select more than one poll option. The default is `false`. |
| `allows_revoting` | boolean | No | This is true when a voter can change an existing vote. For a regular poll, the default is true. For a quiz, the value must be false. |
| `close_at` | string | No | This records when the poll closes automatically, in RFC 3339 format. The format is `date-time`. |
| `correct_option_ids` | Array of integer | No | Supply the zero-based indexes of the correct input options. After creation, poll responses use UUIDs instead. Use this only for a quiz. Supply valid option indexes in strictly increasing order. The minimum is 1 item. The maximum is 12 items. Each item must be unique. |
| `correct_option_ids[]` | integer | Per item | The minimum is 0. The maximum is 11. |
| `country_codes` | Array of string | No | This defines a country filter for poll voters. Omit this field. Supplying it returns capability_not_available. |
| `description` | string | No | This is the description displayed with the poll question. You can use up to 1,024 UTF-16 code units after markup is parsed. |
| `description_entities` | Array of [Entity](entity.md) | No | These define explicit formatting for the description. |
| `description_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the description. Use this instead of description_entities. |
| `explanation` | string | No | This explains the correct quiz answer. You can use up to 200 UTF-16 code units after markup is parsed. |
| `explanation_entities` | Array of [Entity](entity.md) | No | These define explicit formatting for the quiz explanation. |
| `explanation_format` | `"plain"` or `"markdown"` or `"html"` | No | This is the markup format used to parse the quiz explanation. Use this instead of explanation_entities. |
| `explanation_media` | [InputPollMedia](inputpollmedia.md) | No | This contains media displayed with the quiz explanation. |
| `hide_results_until_closes` | boolean | No | This is true when poll results remain hidden until the poll closes. The default is `false`. |
| `is_anonymous` | boolean | No | This is true when votes are anonymous. Non-anonymous polls may identify voters in poll_answer updates. The default is `true`. |
| `is_closed` | boolean | No | This is true when the poll is closed to further votes. The default is `false`. |
| `media` | [InputPollMedia](inputpollmedia.md) | No | This contains attached media or the named media sources for a rich message. |
| `members_only` | `false` | No | This is true when voting is restricted to chat members. This field must be false. |
| `open_period_seconds` | integer | No | Set how long the poll remains open, in seconds, from 5 through 2,628,000 (about 30 days). Use this instead of close_at. |
| `options` | Array of [InputPollOption](inputpolloption.md) | Yes | These are the poll options in display order. The minimum is 1 item. The maximum is 12 items. |
| `question` | string | Yes | This is the question displayed at the top of the poll. You can use up to 300 UTF-16 code units after markup is parsed. |
| `question_entities` | Array of [Entity](entity.md) | No | These define formatting for the poll question. Omit this field or send an empty array. Nonempty entity lists are not supported for this text. The maximum is 0 items. |
| `shuffle_options` | boolean | No | This is true when poll options are shuffled for voters. The default is `false`. |
| `type` | `"regular"` or `"quiz"` | No | The poll kind: regular for a vote without a correct answer, or quiz for a poll with correct options. The default is `"regular"`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
