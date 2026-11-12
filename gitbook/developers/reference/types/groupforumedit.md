# GroupForumEdit

Change a group forum topic's name or icon.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `expected_version` | string | No | Optional current metadata version. A stale value refuses the change; omission freezes the current version during acceptance. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-3]\|18446744073709551614)$`. |
| `icon_key` | `""` or `"chat"` or `"book"` or `"idea"` or `"code"` or `"heart"` or `"star"` | No | The new topic icon key. Omit it to preserve the current icon, or send an empty string to clear it. |
| `name` | `""` or string | No | The forum topic title, from 1 through 128 characters. Omit it or send an empty string to keep the current title. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
