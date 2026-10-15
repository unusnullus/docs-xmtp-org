# CallbackAnswer

The notification, alert, or bot start link used to answer an inline button press.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `cache_seconds` | integer | No | This specifies how long the client may cache this callback answer, in seconds. The minimum is 0. The maximum is 3,600. The default is `0`. |
| `show_alert` | boolean | No | This is true when the callback answer appears as an alert instead of a notification. The default is `false`. |
| `text` | string | No | The plain notification text shown after the button press, up to 200 UTF-16 code units. Set show_alert to display it as an alert instead. Markup is not parsed. |
| `url` | string | No | A start link for your own bot in the form https://www.arx.pro/bot/&lt;handle>?start=&lt;payload>. The payload must contain 1 to 64 ASCII letters, digits, underscores, or hyphens. Other destinations are rejected. Use a start link for your bot's current handle. Other bots and external URLs are not accepted. An invalid URL returns invalid_parameter with field set to url. The value must match `^https://www\.arx\.pro/bot/[A-Za-z0-9_]+\?start=[A-Za-z0-9_-]{1,64}$`. The maximum length is 2,048 characters. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
