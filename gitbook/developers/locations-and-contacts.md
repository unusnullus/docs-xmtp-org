---
description: "Send places, update live positions and request a contact card with consent."
---

# Locations and contacts

You can send a location, a named venue or a contact card in a private chat. Ask users to share their position or their own ARX card with a reply keyboard, and explain how your bot will use that data.

## Send a location <a href="#send-a-location" id="send-a-location"></a>

Use `messages.send` with `content.type: location`. The same content becomes a live location when you include `live`.

Set `ARX_BOT_TOKEN`, `CHAT_ID` and `REQUEST_KEY` before running the example. Use a private chat that the user has started. See [Making requests](making-requests.md) for choosing keys and retrying requests.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
if (!token || !key || !chatId) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const result = await bot.messagesSend(chatId, {
		key,
		body: {
			content: {
				type: "location",
				latitude: 14.62,
				longitude: -90.52,
				horizontal_accuracy: 25,
			},
		},
	});
	console.log(result);
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models

chat_id = os.environ["CHAT_ID"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    request = models.SendMessage(
        content=models.InputContent(
            root=models.InputLocation(
                type="location",
                latitude=14.62,
                longitude=-90.52,
                horizontal_accuracy=25.0,
            )
        ),
    )
    result = bot.messages_send(
        chat_id,
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
    print(result)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, models, presence::Presence};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let request = models::SendMessage {
        content: models::InputContent::InputLocation(Box::new(models::InputLocation {
            horizontal_accuracy: Some(25.0),
            latitude: 14.62,
            live: None,
            longitude: -90.52,
            r#type: models::InputLocationType::Location,
        })),
        effect_id: None,
        link_preview: Presence::Unset,
        protect_content: None,
        reply_markup: None,
        reply_to: None,
        silent: None,
    };
    let result = bot
        .messages_send(&chat_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

| Location field | Rule |
| --- | --- |
| `latitude` | Required. Use a finite number from -90 to 90. |
| `longitude` | Required. Use a finite number from -180 to 180. |
| `horizontal_accuracy` | Optional. Use 0 to 1,500 meters. |

The accepted message contains `content.location` with the resolved location fields.

## Send a venue <a href="#send-a-venue" id="send-a-venue"></a>

A venue adds a required `title` and `address` to coordinates. Its title contains 1 to 256 UTF-16 units and its address 1 to 1,024. Neither can be blank or contain only whitespace. `provider` is optional, but when you include it, all three of `name`, `place_id` and `place_type` are required. For HTTP:

```json
{
  "content": {
    "type": "venue",
    "latitude": 14.62,
    "longitude": -90.52,
    "title": "Meeting point",
    "address": "Main entrance",
    "provider": {
      "name": "other",
      "place_id": "meeting-main-entrance",
      "place_type": "meeting_point"
    }
  }
}
```

`provider.name` is `foursquare`, `google` or `other`. `place_id` and `place_type` each contain 1 to 256 UTF-16 units and cannot be blank or contain only whitespace. A venue cannot have live settings. The response contains `content.venue`.

## Start a live location <a href="#start-a-live-location" id="start-a-live-location"></a>

For HTTP, add `live` to a location:

```json
{
  "content": {
    "type": "location",
    "latitude": 14.62,
    "longitude": -90.52,
    "live": {
      "period_seconds": 900,
      "heading": 180,
      "proximity_alert_radius": 250
    }
  }
}
```

| Live field | Rule |
| --- | --- |
| `period_seconds` or `indefinite: true` | Choose exactly one. A new period is 60 to 86,400 seconds (one minute to one day). |
| `heading` | Optional integer from 1 to 360 degrees. |
| `proximity_alert_radius` | Optional integer from 1 to 100,000 meters. |

ARX returns `expires_at`, which is null for an indefinite location. Do not submit it as input. Other optional input fields reject null.

## Update or stop a live location <a href="#update-or-stop-a-live-location" id="update-or-stop-a-live-location"></a>

Call `messages.edit` for your bot's active live location. Use the last returned `revision` as `expected_revision` and provide both coordinates. This HTTP body updates a message at revision `1`:

```json
{
  "expected_revision": "1",
  "live_location": {
    "latitude": 14.621,
    "longitude": -90.521,
    "heading": 185
  }
}
```

The typed methods are `messagesEdit` in TypeScript and `messages_edit` in Python and Rust. Use the `revision` returned by your send or last edit for the next request. A stale edit returns HTTP 409 `revision_conflict`.

Omitting accuracy, heading, proximity radius or duration keeps its accepted value. You can make one changed live edit per five seconds per message, in addition to the normal [edit limits](limits.md). A faster change returns `rate_limited` with a retry delay. An identical edit keeps the revision and does not consume that live edit allowance.

To extend a finite location, send the new total `period_seconds`, measured from its original start. You can add at most 86,400 seconds per edit, and the new expiry must be in the future and no more than 90 days from the edit. `indefinite: true` removes the expiry and cannot accompany `period_seconds`.

Call `messages.stop_live_location` with body `{}` to keep the last position and end live sharing. You can include an inline `reply_markup` replacement using the [keyboard rules](keyboards.md#edit-or-remove-inline-buttons). Stopping or expiry removes `live`. Movement, stopping and expiry advance `revision` and `state_version` without adding `edited_at`.

Copying or forwarding a live location creates a static location. Retrying an accepted request with its original idempotency key preserves the original result, including its expiry. See [Making requests](making-requests.md) for retry rules.

## Request an ARX card with consent <a href="#request-an-arx-card-with-consent" id="request-an-arx-card-with-consent"></a>

Send a `request_contact` reply button so the user can choose to share their own ARX card. The HTTP body is:

```json
{
  "content": {"type":"text","text":"Share your ARX card to continue."},
  "reply_markup": {
    "type": "reply_keyboard",
    "one_time": true,
    "rows": [[{"type":"request_contact","text":"Share my ARX card"}]]
  }
}
```

Your bot receives a contact message containing the consenting user's `user_id`, `name` and `username` (their handle, which can be null). It does not contain their phone number, email address or wallet address. [Keyboards](keyboards.md#reply-keyboards) covers keyboard behavior and location requests.

Your bot can send an ARX account card for:

- Its own account.
- A user who has started a chat with it.
- A user shared through a `request_users` selection while that sharing consent remains valid.

For a test bot, the chat or selection must come from the bot owner or a current tester. Include the account's user ID. For HTTP, a contact body looks like this:

```json
{"content":{"type":"contact","user_id":"4398046511100"}}
```

ARX resolves the name and handle. Supplying a name cannot override that identity. The request rejects `username`, phone, vCard and wallet fields for this variant. If you cannot share the account, the API returns HTTP 403 `contact_not_shareable`.

## Send a manual card <a href="#send-a-manual-card" id="send-a-manual-card"></a>

A manual contact is text supplied by your bot, without a verified ARX identity. For HTTP:

```json
{
  "content": {
    "type": "contact",
    "name": "Ana López",
    "phone_number": "+50255550100"
  }
}
```

Both `name` and `phone_number` are required, with 1 to 64 UTF-16 units each. You can add `vcard` with 1 to 2,048 UTF-8 bytes. A vCard can contain carriage returns, newlines and tabs, but no other C0 controls. A bot cannot send a manual card using only a vCard.

Do not add `user_id` or `username` to a manual card. Contact inputs reject unknown fields and null. Keep only the contact or location data your bot needs and describe its use in your privacy policy.

## Proximity events <a href="#proximity-events" id="proximity-events"></a>

When an active location enters another person's alert radius, your bot can receive a service message with `type: proximity_alert_triggered`, `traveler`, `watcher` and `distance` in rounded meters. One crossing emits one event. Moving beyond 1.1 times the radius allows a later entry to emit another.

Changing only heading or duration does not create a crossing. A location report cannot prove physical presence. Continue with [Updates](updates.md) to handle these service messages, or [the interactive reference](reference/interactive.md) for live location operations.
