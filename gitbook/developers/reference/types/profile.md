# Profile

Your bot's default profile text and localizations.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `default` | [ProfileText](profiletext.md) | Yes | This is the profile text used when no localization matches the user's language. |
| `localizations` | object | Yes | This contains localized profile text, keyed by language tag. |
| `localizations.*` | [ProfileText](profiletext.md) | No |  |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
