# GroupForumDeletion

Progress of an accepted group topic purge. Acceptance and completed deletion are distinct states.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `chat_id` | string | Yes | The ID of the group containing this topic. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-4]\|18446744073709551615)$`. |
| `lifecycle_state` | `"deleting"` or `"deleted"` | Yes | deleting after durable acceptance, or deleted after the entire topic purge finishes. |
| `purge_complete` | boolean | Yes | True only after every message was removed and the topic and its original purge job completed atomically. False means cleanup was accepted and remains in progress. |
| `topic_id` | string | Yes | The permanent topic ID within this group. Topic 1 identifies General. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-4]\|18446744073709551615)$`. |
| `version` | string | Yes | Current metadata version, encoded as a decimal string. The value must match `^(?:[1-9][0-9]{0,18}\|1[0-7][0-9]{18}\|18[0-3][0-9]{17}\|184[0-3][0-9]{16}\|1844[0-5][0-9]{15}\|18446[0-6][0-9]{14}\|184467[0-3][0-9]{13}\|1844674[0-3][0-9]{12}\|184467440[0-6][0-9]{10}\|1844674407[0-2][0-9]{9}\|18446744073[0-6][0-9]{8}\|1844674407370[0-8][0-9]{6}\|18446744073709[0-4][0-9]{5}\|184467440737095[0-4][0-9]{4}\|18446744073709550[0-9]{3}\|18446744073709551[0-5][0-9]{2}\|1844674407370955160[0-9]{1}\|1844674407370955161[0-4]\|18446744073709551615)$`. |

You receive one of the following forms. Required fields apply within each form.

**When `lifecycle_state` is `"deleting"` and `purge_complete` is `false`:**

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `lifecycle_state` | `"deleting"` | No | deleting after durable acceptance, or deleted after the entire topic purge finishes. |
| `purge_complete` | `false` | No | True only after every message was removed and the topic and its original purge job completed atomically. False means cleanup was accepted and remains in progress. |

**When `lifecycle_state` is `"deleted"` and `purge_complete` is `true`:**

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `lifecycle_state` | `"deleted"` | No | deleting after durable acceptance, or deleted after the entire topic purge finishes. |
| `purge_complete` | `true` | No | True only after every message was removed and the topic and its original purge job completed atomically. False means cleanup was accepted and remains in progress. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
