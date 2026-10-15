---
description: "Configure command lists, menu buttons and localized profile text."
---

# Commands & profile

You can give users a command menu and a profile that explains what your bot does. Configure command lists for the chats and languages you support, then implement the corresponding message handlers in your bot.

## Set commands <a href="#set-commands" id="set-commands"></a>

Call `commands.set` with a complete replacement list. Set `ARX_BOT_TOKEN` and `REQUEST_KEY` before running this example. It configures English commands for private chats.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
if (!token || !key) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const result = await bot.commandsSet({
		key,
		query: { scope: "all_private_chats", language_code: "en" },
		body: {
			commands: [
				{
					command: "start",
					description: "Start a conversation",
				},
				{
					command: "help",
					description: "Show available actions",
				},
			],
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

request = models.SetCommands.model_validate(
    {
        "commands": [
            {"command": "start", "description": "Start a conversation"},
            {"command": "help", "description": "Show available actions"},
        ]
    }
)
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    result = bot.commands_set(
        query={"scope": "all_private_chats", "language_code": "en"},
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
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let request: models::SetCommands = serde_json::from_value(serde_json::json!({
      "commands": [
        {
          "command": "start",
          "description": "Start a conversation"
        },
        {
          "command": "help",
          "description": "Show available actions"
        }
      ]
    }))?;
    let result = bot
        .commands_set(
            &[("scope", "all_private_chats"), ("language_code", "en")],
            Some(&request),
            Some(&key),
        )
        .await?;
    println!("{}", serde_json::to_string(&result)?);
    Ok(())
}
```

{% endtab %}
{% endtabs %}

A list can contain up to 100 commands. Each command requires `command` and `description`. Names are unique within the list and contain 1 to 32 lowercase ASCII letters, digits or underscores, without the leading slash. Descriptions contain 1 to 256 UTF-16 units and cannot be blank.

Configuring the list does not implement the commands or restrict what users can type. Your bot still receives command messages through [Updates](updates.md). Check command arguments and the user's permissions in your handler. The SDK routers can help you match command names.

## Scope and language <a href="#scope-and-language" id="scope-and-language"></a>

`commands.get`, `commands.set` and `commands.delete` use the same query fields:

| Query field | Rule |
| --- | --- |
| `scope` | Defaults to `default`. Use `all_private_chats` for private chats or `chat` for a specific chat. |
| `chat_id` | Required for `chat`, `chat_administrators` and `chat_member`; omit it for other scopes. |
| `user_id` | Required only for `chat_member`. It must identify the user in that private chat. |
| `language_code` | Omit it or use an empty string for the fallback list. A localized list uses a valid two-letter lowercase language code such as `en` or `pt`. |

The API also accepts the group scopes `all_group_chats`, `all_chat_administrators` and `chat_administrators`. They don't affect private chats. See [Not available yet](not-available-yet.md) for supported chat capabilities.

For a private chat, ARX checks `chat`, then `all_private_chats`, then `default`. At each scope it tries the requested language before that scope's fallback list. The first configured list wins, including an empty list. For example, a chat-specific fallback takes precedence over a language-specific global list.

`commands.get` reads one exact scope and language. Use `commands.resolve` to inspect the list the user would see. It returns `commands`, `resolved_scope` and `resolved_language`. You can provide `chat_id` and `language_code`. If you also supply `user_id`, it must match the user in that private chat.

## Clear a command list <a href="#clear-a-command-list" id="clear-a-command-list"></a>

Send `{"commands":[]}` to `commands.set` to configure an intentionally empty list. That list stops fallback at its scope and language. Call `commands.delete` to remove the configured list and let resolution continue to a broader scope or fallback language. Its optional body is `{}`.

Follow [Making requests](making-requests.md) when retrying an uncertain command update.

## Configure the menu button <a href="#configure-the-menu-button" id="configure-the-menu-button"></a>

Call `menu_button.set` with `{"type":"commands"}` to show the command menu. Use `{"type":"default"}` to restore the default behavior. Omit its `chat_id` query for the bot-wide setting, or provide an accessible private chat ID for a chat-specific setting.

`menu_button.get` returns the configured setting for the selected chat or the bot-wide setting when you omit `chat_id`. A per-chat `default` setting falls back to the bot-wide button. Use [Keyboards](keyboards.md) when you need buttons attached to a particular message instead.

## Update profile text <a href="#update-profile-text" id="update-profile-text"></a>

Call `profile.update` with at least one of `name`, `description` or `short_description`. Use a fresh `REQUEST_KEY` for this operation. This example changes the default descriptions while keeping the name.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
if (!token || !key) {
	throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const result = await bot.profileUpdate({
		key,
		body: {
			description: "Plan meetings and keep track of shared tasks.",
			short_description: "Meeting planning and task reminders.",
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

request = models.PatchProfile.model_validate(
    {
        "description": "Plan meetings and keep track of shared tasks.",
        "short_description": "Meeting planning and task reminders.",
    }
)
with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    result = bot.profile_update(
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
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let request: models::PatchProfile = serde_json::from_value(serde_json::json!({
      "description": "Plan meetings and keep track of shared tasks.",
      "short_description": "Meeting planning and task reminders."
    }))?;
    let result = bot.profile_update(&[], Some(&request), Some(&key)).await?;
    println!("{}", serde_json::to_string(&result)?);
    Ok(())
}
```

{% endtab %}
{% endtabs %}

| Profile field | Limit and behavior |
| --- | --- |
| `name` | Up to 64 UTF-16 units. The default name must contain non-whitespace text and cannot be cleared. |
| `description` | Up to 512 UTF-16 units. |
| `short_description` | Up to 120 UTF-16 units. |
| `language_code` | Omit it or use an empty string for default text. A localization uses a BCP 47 tag with at most 32 characters, such as `en` or `pt-BR`. ARX normalizes the tag to lowercase. |

Omitting a text field preserves it. Null or an empty string clears a description or a localized field, allowing fallback. At least one text field must be present; `language_code` alone is not an update. Names reject control characters; descriptions allow newline and tab. A name that fails the naming policy returns HTTP 400 `name_not_allowed`.

Profile language tags can include a region; command language codes use only two letters. Profile fallback happens separately for each field: the full requested tag, its base language, then the default. Clearing a localized description does not clear the localized name.

`profile.get` returns `default` and all `localizations`. Its optional `language_code` query validates a tag but does not reduce the returned set. A successful update returns the accepted fields and `profile_version` and `config_version`; it does not arrive as a conversation update.

## Profile photos <a href="#profile-photos" id="profile-photos"></a>

Use `profile.set_photo` to replace your bot's profile media. [Profile media in Files](files.md#profile-media) covers request bodies, allowed media, exact size limits and upload helpers. Use `profile.delete_photo` with `{}` to remove the current photo.

[The profile reference](reference/profile.md) gives typed methods for commands, menus, text and photos. Continue with [Deep links](deep-links.md) to choose a conversation step when a user starts your bot.
