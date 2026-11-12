# Contact

An account contact card or a manual contact card returned in a message.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `name` | string | Yes | This is the display name of the user, bot, contact, or provider. |
| `phone_number` | string | No | You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `public_address` | string | No | The account public address, included only when its owner explicitly chooses to share it through a Mini App. The minimum length is 1 character. |
| `user_id` | string | No | This is the user ID, encoded as a decimal string. A user ID differs from a private chat ID. The value must match `^[1-9][0-9]*$`. |
| `username` | string or null | No | This is the handle of the user or bot. The value may be absent or null when no handle is available. |
| `vcard` | string | No | You can supply up to 2,048 UTF-8 bytes. Control characters are rejected except for carriage return, line feed, and tab. The vCard text is not verified. The minimum length is 1 character. |

You receive one of the following forms. Required fields apply within each form.

- The response includes `user_id` and `username`. The response omits `phone_number` and `vcard`.

- The response omits `user_id`, `username`, and `public_address`. The response includes at least one of `phone_number` or `vcard`.

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
