---
description: "Turn on Business connections, Manage bots and Guest queries for your bot, and check which modes it can use."
---

# Advanced modes

Advanced modes let your bot work outside its own private chats. A bot can assist a person inside that person's own chats, create and run bots for people who ask for one, or answer a single message in a chat where it is not a member. Each mode is off until you, as the bot owner, accept its terms in NexoCreator.

| Mode in NexoCreator | Setting on your bot | What your bot can do |
| --- | --- | --- |
| Business connections | `can_connect_to_business` | Receive and answer a person's private chats on their behalf, in the chats they choose. |
| Manage bots | `can_manage_bots` | Create bots for people who ask, then read and rotate their tokens and limit who can use them. |
| Guest queries | `supports_guest_queries` | Answer one message in a chat where your bot is not a member, when someone mentions it. |
| Bot conversations | `can_talk_to_bots` | Exchange messages with other bots that also turned this mode on. ARX applies loop limits to these conversations. This section does not cover the mode in detail. |

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Business connections</strong></td><td>What people grant, what your bot receives and what it can send.</td><td><a href="managed-bots/business-connections.md">Business connections</a></td></tr><tr><td><strong>Business endpoint</strong></td><td>Run the encrypted endpoint and send automatic replies.</td><td><a href="managed-bots/business-endpoint.md">Business endpoint</a></td></tr><tr><td><strong>Managed bots</strong></td><td>Create bots for people and manage their tokens and access.</td><td><a href="managed-bots/managed-bots.md">Managed bots</a></td></tr><tr><td><strong>Guest queries</strong></td><td>Answer a mention in a chat your bot has not joined.</td><td><a href="managed-bots/guest-queries.md">Guest queries</a></td></tr></tbody></table>

## Turn on a mode <a href="#turn-on-a-mode" id="turn-on-a-mode"></a>

1. Open NexoCreator and select your bot.
2. Open **Advanced modes**. The row shows how many modes are on.
3. Select a mode and choose **Review terms**.
4. Read the terms and choose **Accept and turn on**.
5. For Business connections, enter the operator and data processors, then choose **Save declaration and turn on**. See [Declare your operator and processors](#declare-your-operator-and-processors).

**Advanced modes** lists only the modes that ARX currently offers for your bot, each with its own terms version. If the screen is missing or empty, no mode is available for your bot yet. Accepting a mode also requires that you accepted the current ARX developer terms; NexoCreator shows them first if needed. A mode marked **Terms needed** stays off until you accept its current terms. You can turn a mode off at any time from the same screen.

Turning a mode off has these effects:

| Mode | When you turn it off |
| --- | --- |
| Business connections | Every person connected to your bot is disconnected and told that the bot stopped. NexoCreator shows how many connections will end before you confirm. |
| Manage bots | Your bot can no longer create managed bots or use the managed-bot methods. |
| Guest queries | Your bot stops receiving guest requests from other chats. |

## Declare your operator and processors <a href="#declare-your-operator-and-processors" id="declare-your-operator-and-processors"></a>

Before Business connections can be turned on, NexoCreator asks for:

- **Name shown to people**: the person or company people can contact about their data.
- **Data processors**: every service that receives people's messages through your bot, such as a hosting provider or an AI service. Give each one a name, what it is used for, and an HTTPS privacy policy link.
- A confirmation that there are no other processors, when the list is empty.

People see the operator and processor list before they connect your bot. Keep it current when you change providers.

## Check modes from your code <a href="#check-modes-from-your-code" id="check-modes-from-your-code"></a>

`me.get` returns the effective value of each mode in `can_connect_to_business`, `can_manage_bots`, `supports_guest_queries` and `can_talk_to_bots`. A `true` value means you turned the mode on. Platform availability still applies to every call, so handle the errors each mode documents even when the value is `true`.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
if (!token) throw new Error("Set ARX_BOT_TOKEN");
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const me = await bot.meGet();
	console.log({
		business: me.can_connect_to_business === true,
		manageBots: me.can_manage_bots === true,
		guest: me.supports_guest_queries === true,
	});
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    me = bot.me_get()
    print(
        {
            "business": me.can_connect_to_business is True,
            "manage_bots": me.can_manage_bots is True,
            "guest": me.supports_guest_queries is True,
        }
    )
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let me = bot.me_get(&[], None, None).await?;
    println!(
        "business={} manage_bots={} guest={}",
        me.can_connect_to_business == Some(true),
        me.can_manage_bots == Some(true),
        me.supports_guest_queries == Some(true),
    );
    Ok(())
}
```

{% endtab %}
{% endtabs %}

## Rules for every mode <a href="#rules-for-every-mode" id="rules-for-every-mode"></a>

- **Consent comes from people, not from your bot.** A person connects your bot, confirms a managed bot, or mentions your bot. No method lets your bot start any of these on its own.
- **Updates carry no extra access.** A `business_connection`, `managed_bot` or `guest_message` update does not let your bot read chat history, join a chat or message the person in a regular private chat.
- **Send an idempotency key with changes that accept one.** Token rotation, access settings, Business sends and Business profile changes require them. Use 1 to 64 ASCII letters, digits, underscores or hyphens, and keep the same key and body when you retry. See [Making requests](making-requests.md).
- **Do not retry mode errors automatically.** Errors such as `managed_mode_disabled`, `managed_bot_detached`, `query_expired` or `business_reply_window_closed` need a change in state, not another attempt.
- **Keep secrets out of logs.** This includes managed bot tokens, your Business endpoint state key and decrypted Business messages.

All three modes deliver their updates through polling or your webhook. `managed_bot`, `guest_message`, `business_connection` and `business_envelope` are in the default update filter. If you set `allowed_updates`, include the types you need. See [Polling and webhooks](updates.md#filters).
