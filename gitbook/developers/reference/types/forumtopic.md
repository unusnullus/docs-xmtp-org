# ForumTopic

Metadata and current capabilities of a group forum topic.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `capabilities` | [ForumTopicCapabilities](forumtopiccapabilities.md) | Yes | Operations currently available to your bot for this topic. |
| `icon_color` | `7322096` or `16766590` or `13338331` or `9367192` or `16749490` or `16478047` | Yes | The RGB icon color, chosen from the supported topic colors. |
| `icon_key` | string | No | The topic's current custom icon key, when one is set. The value must match `^[!-~]{1,64}$`. |
| `is_closed` | boolean | Yes | Whether this topic is closed. |
| `is_hidden` | boolean | Yes | Whether this topic is hidden. |
| `is_name_implicit` | boolean | Yes | True when a localized default supplies the topic title. |
| `last_message_id` | string | Yes | The highest message ID accepted in this topic, including service messages. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-4]\|18446744073709551615)$`. |
| `name` | string | Yes | The forum topic title, from 1 through 128 characters. The value must match `\S`. |
| `topic_id` | string | Yes | The permanent topic ID within this group. Topic 1 identifies General. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-4]\|18446744073709551615)$`. |
| `version` | string | Yes | Current metadata version, encoded as a decimal string. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-4]\|18446744073709551615)$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
