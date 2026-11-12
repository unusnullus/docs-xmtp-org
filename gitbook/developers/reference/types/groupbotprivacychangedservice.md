# GroupBotPrivacyChangedService

An immediate privacy-mode change. All active bot members receive this platform service, including the affected bot.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `access` | `"all_messages"` or `"addressed_only"` | Yes | The bot's new effective group message access. |
| `bot` | [User](user.md) | Yes | The bot whose group privacy setting changed. |
| `type` | `"bot_privacy_changed"` | Yes | The service or update type selecting this payload. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
