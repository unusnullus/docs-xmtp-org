---
description: "Start your bot with a parameter and connect an ARX user to your application."
---

# Deep links

You can open your bot inside ARX with a Start parameter, then use that parameter to choose a conversation step or link an account. Put the link in an inline URL button, or return it when you answer one of your bot's callbacks.

## Start payloads <a href="#start-payloads" id="start-payloads"></a>

Use `https://www.arx.pro/bot/<handle>?start=<payload>` with your bot's current handle, for example `https://www.arx.pro/bot/ACMESUPPORTBOT?start=help_42`. The parameter contains 1 to 64 ASCII letters, digits, underscores or hyphens. Use unpadded base64url when you need to encode a compact value.

The user must tap Start, even if the chat already exists. That action unblocks a blocked bot and sends a `/start` message. Your bot receives `message.start_parameter` and message text such as `/start help_42`. An invalid parameter, including one with `=` padding, opens the bot without passing the value.

Treat `start_parameter` as input from a user. It expires 24 hours after Start. A late delivery keeps the update ID and `/start` text but omits the parameter.

## Callback links <a href="#callback-links" id="callback-links"></a>

A callback answer can open your own bot with a Start parameter. Set `CALLBACK_QUERY_ID` from a recent callback's `id`, `ARX_BOT_TOKEN`, and a new `REQUEST_KEY`. The example calls `me.get` to obtain your bot's current handle and builds its own Start link.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const callbackId = process.env.CALLBACK_QUERY_ID;
if (!token || !key || !callbackId) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CALLBACK_QUERY_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const me = await bot.meGet();
	const result = await bot.callbackQueriesAnswer(callbackId, {
		key,
		body: { url: `https://www.arx.pro/bot/${me.username}?start=help_42` },
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

callback_id = os.environ["CALLBACK_QUERY_ID"]
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    me = bot.me_get()
    request = models.CallbackAnswer(
        url=f"https://www.arx.pro/bot/{me.username}?start=help_42"
    )
    result = bot.callback_queries_answer(
        callback_id,
        body=request,
        key=os.environ["REQUEST_KEY"],
    )
    print(result)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let callback_id = std::env::var("CALLBACK_QUERY_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let me = bot.me_get(&query, None, None).await?;
    let request = models::CallbackAnswer {
        url: Some(format!(
            "https://www.arx.pro/bot/{}?start=help_42",
            me.username
        )),
        ..Default::default()
    };
    let result = bot
        .callback_queries_answer(&callback_id, &query, Some(&request), Some(&key))
        .await?;
    println!("{result:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

Callback navigation requires HTTPS, the host `www.arx.pro`, your bot's current path (such as `/bot/ACMESUPPORTBOT`), and exactly one valid `start` parameter. The handle comparison ignores case. The URL can contain at most 2,048 UTF-8 bytes and cannot include a port, credentials, a fragment or extra parameters.

An invalid target returns HTTP 400 `invalid_parameter`, with `field: url` and `reason: invalid_url`. The failed request does not consume the answer; correct the target while the query remains valid. For answer deadlines and other callback fields, see [Keyboards and callbacks](keyboards.md#receive-and-answer-a-callback).

## Link an account <a href="#link-an-account" id="link-an-account"></a>

Generate a short-lived, opaque code that you can use only once. Store its account and purpose in your application, then put the code in `start`. When you receive it, validate the code and the ARX user ID before linking the accounts. Never put a token, email address or other personal data in the URL.

Keep the account-linking action idempotent because [updates can repeat](updates.md#order-and-duplicates). Ask the user to confirm the account you are about to link when the action changes access to their data.

For message buttons that carry these links, continue with [Keyboards](keyboards.md). Other link entry points are listed on [Not available yet](not-available-yet.md).
