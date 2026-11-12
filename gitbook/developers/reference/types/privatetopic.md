# PrivateTopic

Private topic metadata. Its permanent ID is the creation message ID.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `icon_color` | `7322096` or `16766590` or `13338331` or `9367192` or `16749490` or `16478047` | Yes | The fixed RGB icon color, chosen from the supported colors. |
| `icon_key` | string | No | An icon key from topics.list_icons. An empty value in an edit removes the custom icon. The value must match `^[!-~]{1,64}$`. |
| `is_name_implicit` | boolean | Yes | True when a default name was supplied because the user left the name empty. |
| `last_message_id` | string | Yes | The highest message ID accepted in this topic, including service messages. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-4]\|18446744073709551615)$`. |
| `name` | string | Yes | The topic name, from 1 through 128 characters. The value must match `\S`. |
| `topic_id` | string | Yes | The creation message ID that permanently identifies this private topic. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-4]\|18446744073709551615)$`. |
| `version` | string | Yes | Current metadata version, encoded as a decimal string. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-4]\|18446744073709551615)$`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
