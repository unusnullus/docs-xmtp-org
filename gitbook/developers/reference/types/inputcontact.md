# InputContact

The account ID or manual contact details to send as a contact card. For an account card, supply the ID of a user available to your bot. The response uses that account's name and handle, and ignores a supplied name. For a manual card, supply name and phone_number, with an optional vCard. Neither input variant accepts username or public_address.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `name` | string | No | This is the display name of the user, bot, contact, or provider. You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `phone_number` | string | No | This is the phone number displayed on a manual contact card. You can use up to 64 UTF-16 code units after markup is parsed. The minimum length is 1 character. |
| `type` | `"contact"` | Yes | The content type is contact. |
| `user_id` | string | No | This is the user ID, encoded as a decimal string. A user ID differs from a private chat ID. The value must match `^[1-9][0-9]*$`. |
| `vcard` | string | No | This contains vCard text supplied with a manual contact card. You can supply up to 2,048 UTF-8 bytes. Control characters are rejected except for carriage return, line feed, and tab. The vCard text is not verified. The minimum length is 1 character. |

Supply exactly one of the following forms. Required fields apply within each form.

- Include `user_id`. Omit `phone_number` and `vcard`.

- Include `name` and `phone_number`. Omit `user_id`.

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
