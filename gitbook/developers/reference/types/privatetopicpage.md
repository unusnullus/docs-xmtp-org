# PrivateTopicPage

Active private topics in ascending creation message ID order.

You receive one of the following forms. Required fields apply within each form.

**When `has_more` is `true`, `next_after_topic_id` has the type string, and `topics` has the type Array of [PrivateTopicTopic](privatetopictopic.md):**

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `has_more` | `true` | Yes | True when another page may follow. A full page can require an empty final request. |
| `next_after_topic_id` | string | Yes | Use this last returned topic ID as after_topic_id for the next page. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-4]\|18446744073709551615)$`. |
| `topics` | Array of [PrivateTopicTopic](privatetopictopic.md) | Yes | The active private topics in ascending creation message ID order. The minimum is 1 item. The maximum is 100 items. |

**When `has_more` is `false` and `topics` has the type Array of [PrivateTopicTopic](privatetopictopic.md):**

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `has_more` | `false` | Yes | True when another page may follow. A full page can require an empty final request. |
| `topics` | Array of [PrivateTopicTopic](privatetopictopic.md) | Yes | The active private topics in ascending creation message ID order. The maximum is 100 items. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
