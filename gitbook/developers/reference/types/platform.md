# Platform

The capabilities, supported media and reactions, effects, and limits reported by ARX.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `api_revision` | string | Yes | This is the revision of the API capabilities returned by the platform. The value must match `^[0-9]+$`. |
| `dice_emoji` | Array of string | Yes | You can use these emoji to send dice messages. |
| `limits` | [PlatformLimits](platformlimits.md) | Yes | Use these current platform limits when preparing requests for your bot. |
| `message_effects` | Array of [MessageEffect](messageeffect.md) | Yes | These are the message effects that the platform supports. |
| `reactions` | object | Yes | This contains reactions to apply or the current reaction state. An empty input array removes your bot's reaction. |
| `reactions.allowed_emoji` | Array of string | Yes | You can use these emoji for message reactions. |
| `revision` | string | Yes | This is the version of the platform catalog, encoded as a decimal string. The value must match `^[0-9]+$`. |
| `send_profile` | `"standard"` or `"restricted"` | Yes | This is the send rate profile currently applied to your bot. |
| `webhook_egress_cidrs` | Array of string | Yes | The platform sends webhook requests from these IP ranges. Use them when configuring your endpoint's firewall. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
