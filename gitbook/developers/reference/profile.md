# Commands and profile

Set your bot's command lists, menu button, profile text, and avatar.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`commands.delete`](#operation-commands-delete) | `DELETE /commands` |
| [`commands.get`](#operation-commands-get) | `GET /commands` |
| [`commands.set`](#operation-commands-set) | `PUT /commands` |
| [`commands.resolve`](#operation-commands-resolve) | `GET /commands/resolve` |
| [`default_admin_rights.get`](#operation-default-admin-rights-get) | `GET /default_admin_rights` |
| [`default_admin_rights.set`](#operation-default-admin-rights-set) | `PUT /default_admin_rights` |
| [`menu_button.get`](#operation-menu-button-get) | `GET /menu_button` |
| [`menu_button.set`](#operation-menu-button-set) | `PUT /menu_button` |
| [`profile.get`](#operation-profile-get) | `GET /profile` |
| [`profile.update`](#operation-profile-update) | `PATCH /profile` |
| [`profile.delete_photo`](#operation-profile-delete-photo) | `DELETE /profile/photo` |
| [`profile.set_photo`](#operation-profile-set-photo) | `PUT /profile/photo` |

## commands.delete <a href="#operation-commands-delete" id="operation-commands-delete"></a>

`DELETE /bot-api/v1/commands`

Remove the command list for one exact scope and language. Other lists remain available.

Deleting a more specific list allows a less specific list to apply. Use `commands.resolve` to inspect the effective commands after deletion. See [Commands & profile](../commands-and-profile.md) for precedence and scope fields.

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `scope` | query | `"default"` or `"all_private_chats"` or `"all_group_chats"` or `"all_chat_administrators"` or `"chat"` or `"chat_administrators"` or `"chat_member"` | No | Choose the command scope. chat, chat_administrators, and chat_member require chat_id. Only chat_member requires user_id. Omit the IDs for all other scopes. The default is `"default"`. |
| `chat_id` | query | string | No | Supply the chat ID for chat, chat_administrators, or chat_member scope. Omit it for every other scope. The value must match `^[1-9][0-9]*$`. |
| `user_id` | query | string | No | Supply the user ID for chat_member scope. Omit it for every other scope. The value must match `^[1-9][0-9]*$`. |
| `language_code` | query | string | No | Use a valid two-letter BCP 47 language code, or an empty string for the default command set. The value must match `^([a-z]{2})?$`. |

### Request <a href="#request" id="request"></a>

Content type: `application/json`. You may omit the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response" id="response"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors" id="errors"></a>

HTTP 400 `invalid_parameter` means the scope, language, or scope-specific IDs are invalid. Correct the selection before retrying.

### Example <a href="#example" id="example"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.commandsDelete({ key: requestKey });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.commands_delete(key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.commands_delete(&query, None, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X DELETE \
  "https://signal.arx.pro/bot-api/v1/commands" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": true
}
```

## commands.get <a href="#operation-commands-get" id="operation-commands-get"></a>

`GET /bot-api/v1/commands`

Read the command list for one exact scope and language. This request does not apply fallback rules. Use `commands.resolve` when you need the list a particular user will see.

Omit `scope` to select `default`, and omit `language_code` to select the unlocalized list. Scope fields must match the selected scope. See [Commands & profile](../commands-and-profile.md) for scope selection and language precedence.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `scope` | query | `"default"` or `"all_private_chats"` or `"all_group_chats"` or `"all_chat_administrators"` or `"chat"` or `"chat_administrators"` or `"chat_member"` | No | Choose the command scope. chat, chat_administrators, and chat_member require chat_id. Only chat_member requires user_id. Omit the IDs for all other scopes. The default is `"default"`. |
| `chat_id` | query | string | No | Supply the chat ID for chat, chat_administrators, or chat_member scope. Omit it for every other scope. The value must match `^[1-9][0-9]*$`. |
| `user_id` | query | string | No | Supply the user ID for chat_member scope. Omit it for every other scope. The value must match `^[1-9][0-9]*$`. |
| `language_code` | query | string | No | Use a valid two-letter BCP 47 language code, or an empty string for the default command set. The value must match `^([a-z]{2})?$`. |

### Request <a href="#request-1" id="request-1"></a>

Do not send a request body.

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Commands](types/commands.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 400 `invalid_parameter` means the scope or language is invalid, or a scope-specific ID is missing. Check the scope fields before retrying.

### Example <a href="#example-1" id="example-1"></a>

Set `ARX_BOT_TOKEN` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
if (!arxBotToken) {
  throw new Error("Set ARX_BOT_TOKEN");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.commandsGet();
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.commands_get()
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.commands_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/commands" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "commands": [
      {
        "command": "start",
        "description": "Start",
        "is_ephemeral": false
      }
    ],
    "language_code": "",
    "scope": "default"
  }
}
```

## commands.set <a href="#operation-commands-set" id="operation-commands-set"></a>

`PUT /bot-api/v1/commands`

Replace the command list for one scope and language. The response includes the accepted commands and their configuration versions.

Send the complete list, with up to 100 commands. Each command uses 1 to 32 lowercase ASCII letters, digits, or underscores, without a leading slash. Each description contains 1 to 256 UTF-16 code units. An empty list clears this selection. Use `commands.resolve` to check the result of fallback rules.

See [Commands & profile](../commands-and-profile.md) for scopes, localization, and ephemeral commands.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `scope` | query | `"default"` or `"all_private_chats"` or `"all_group_chats"` or `"all_chat_administrators"` or `"chat"` or `"chat_administrators"` or `"chat_member"` | No | Choose the command scope. chat, chat_administrators, and chat_member require chat_id. Only chat_member requires user_id. Omit the IDs for all other scopes. The default is `"default"`. |
| `chat_id` | query | string | No | Supply the chat ID for chat, chat_administrators, or chat_member scope. Omit it for every other scope. The value must match `^[1-9][0-9]*$`. |
| `user_id` | query | string | No | Supply the user ID for chat_member scope. Omit it for every other scope. The value must match `^[1-9][0-9]*$`. |
| `language_code` | query | string | No | Use a valid two-letter BCP 47 language code, or an empty string for the default command set. The value must match `^([a-z]{2})?$`. |

### Request <a href="#request-2" id="request-2"></a>

Content type: `application/json`. You must include the body.

[SetCommands](types/setcommands.md)

The ordered command list to store for the requested scope and language. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `commands` | Array of [Command](types/command.md) | Yes | These are the commands in the order shown to users. The maximum is 100 items. |

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [CommandsReceipt](types/commandsreceipt.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 400 `invalid_parameter` means a command, scope, language, or scope-specific ID is invalid. Correct the named field and send the complete replacement list.

### Example <a href="#example-2" id="example-2"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.SetCommands = {
    "commands": [{
      "command": "start",
      "description": "Start",
      "is_ephemeral": false
    }]
  };
  const result = await bot.commandsSet({ body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.SetCommands(
        commands=[models.Command(command='start', description='Start', is_ephemeral=False)],
    )
    result = bot.commands_set(body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::SetCommands {
        commands: vec![models::Command {
            command: "start".to_owned(),
            description: "Start".to_owned(),
            is_ephemeral: Some(false),
        }],
    };
    let query = [];
    let result = bot.commands_set(&query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/commands" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"commands": [{"command": "start", "description": "Start", "is_ephemeral": false}]}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "bot_ui_version": "2",
    "chat_id": "",
    "commands": [
      {
        "command": "start",
        "description": "Start",
        "is_ephemeral": false
      }
    ],
    "config_version": "2",
    "language_code": "",
    "scope": "default",
    "user_id": ""
  }
}
```

## commands.resolve <a href="#operation-commands-resolve" id="operation-commands-resolve"></a>

`GET /bot-api/v1/commands/resolve`

Read the effective command list for a user in a chat. Pass a chat ID to resolve commands for a private chat. If you also supply a user ID, it must identify the user in that private chat. You may omit both IDs to resolve the bot-wide lists. Include the user's language when you know it.

The result identifies the selected scope and language. In a private chat, the API checks the chat-specific list, then `all_private_chats`, then `default`, considering the localized list before the unlocalized list at each step.

See [Commands & profile](../commands-and-profile.md) to configure the lists that this request resolves.

### Parameters <a href="#parameters-3" id="parameters-3"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | query | string | No | Optionally supply the private chat ID as a decimal string to include chat-specific commands in resolution. Omit it to resolve without a chat-specific set. The value must match `^[1-9][0-9]*$`. |
| `user_id` | query | string | No | Optionally supply the user ID as a decimal string. This requires chat_id, and the ID must identify the user in that private chat. The value must match `^[1-9][0-9]*$`. |
| `language_code` | query | string | No | Use a valid two-letter BCP 47 language code, or an empty string for the default command set. The value must match `^([a-z]{2})?$`. |

### Request <a href="#request-3" id="request-3"></a>

Do not send a request body.

### Response <a href="#response-3" id="response-3"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [ResolvedCommands](types/resolvedcommands.md) in `result` |

### Errors <a href="#errors-3" id="errors-3"></a>

HTTP 400 `invalid_parameter` means a user ID, chat ID, or language is invalid. Use the IDs from the private chat you want to inspect.

### Example <a href="#example-3" id="example-3"></a>

Set `ARX_BOT_TOKEN`, `CHAT_ID`, `USER_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
const userId = process.env.USER_ID;
if (!arxBotToken || !chatId || !userId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID, USER_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.commandsResolve({ query: {
    "chat_id": chatId,
    "user_id": userId,
    "language_code": "en"
  } });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.commands_resolve(query={'chat_id': os.environ["CHAT_ID"], 'user_id': os.environ["USER_ID"], 'language_code': 'en'})
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let user_id = std::env::var("USER_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [("chat_id", chat_id.as_str()), ("user_id", user_id.as_str()), ("language_code", "en")];
    let result = bot.commands_resolve(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/commands/resolve?chat_id=${CHAT_ID}&user_id=${USER_ID}&language_code=en" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "commands": [
      {
        "command": "start",
        "description": "Start",
        "is_ephemeral": false
      }
    ],
    "resolved_language": "",
    "resolved_scope": "default"
  }
}
```

## default_admin_rights.get <a href="#operation-default-admin-rights-get" id="operation-default-admin-rights-get"></a>

`GET /bot-api/v1/default_admin_rights`

Read the administrator rights your bot suggests for the selected target. Pass `groups` or `channels` as `target`.

These values are suggestions. Reading or saving them does not grant access to a chat. See [Commands & profile](../commands-and-profile.md) for bot configuration and [Not available yet](../not-available-yet.md) for availability.

### Parameters <a href="#parameters-4" id="parameters-4"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `target` | query | `"groups"` or `"channels"` | No | Choose the chat kind for which to read or change the default administrator permissions. The default is `"groups"`. |

### Request <a href="#request-4" id="request-4"></a>

Do not send a request body.

### Response <a href="#response-4" id="response-4"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [AdminRights](types/adminrights.md) in `result` |

### Errors <a href="#errors-4" id="errors-4"></a>

HTTP 400 `invalid_parameter` means `target` is invalid. Use `groups` or `channels`.

### Example <a href="#example-4" id="example-4"></a>

Set `ARX_BOT_TOKEN` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
if (!arxBotToken) {
  throw new Error("Set ARX_BOT_TOKEN");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.defaultAdminRightsGet({ query: {
    "target": "groups"
  } });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.default_admin_rights_get(query={'target': 'groups'})
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [("target", "groups")];
    let result = bot.default_admin_rights_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/default_admin_rights?target=groups" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": null
}
```

## default_admin_rights.set <a href="#operation-default-admin-rights-set" id="operation-default-admin-rights-set"></a>

`PUT /bot-api/v1/default_admin_rights`

Save the administrator rights your bot suggests for the selected target. Use `rights: null` to clear the suggestions.

These values do not grant your bot any permissions. Use only the flags accepted by the request schema. See [Commands & profile](../commands-and-profile.md) for configuration and [Not available yet](../not-available-yet.md) for availability.

### Parameters <a href="#parameters-5" id="parameters-5"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `target` | query | `"groups"` or `"channels"` | No | Choose the chat kind for which to read or change the default administrator permissions. The default is `"groups"`. |

### Request <a href="#request-5" id="request-5"></a>

Content type: `application/json`. You must include the body.

[SetAdminRights](types/setadminrights.md)

The default administrator rights your bot requests for groups or channels. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `rights` | [AdminRights](types/adminrights.md) | Yes | These are the default administrator permissions that your bot requests when added to a chat. |

### Response <a href="#response-5" id="response-5"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-5" id="errors-5"></a>

HTTP 400 `invalid_parameter` means the target or rights are invalid. Check the accepted flags and omit rights that cannot be enabled.

### Example <a href="#example-5" id="example-5"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.SetAdminRights = {
    "rights": {
      "can_delete_messages": true
    }
  };
  const result = await bot.defaultAdminRightsSet({ query: {
    "target": "groups"
  }, body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.SetAdminRights(
        rights=models.AdminRights(root=models.AdminRightsValue(can_delete_messages=True)),
    )
    result = bot.default_admin_rights_set(query={'target': 'groups'}, body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::SetAdminRights {
        rights: Some(models::AdminRightsValue {
            can_change_info: None,
            can_delete_messages: Some(true),
            can_delete_stories: None,
            can_edit_messages: None,
            can_edit_stories: None,
            can_invite_users: None,
            can_manage_chat: None,
            can_manage_direct_messages: None,
            can_manage_tags: None,
            can_manage_topics: None,
            can_manage_video_chats: None,
            can_pin_messages: None,
            can_post_messages: None,
            can_post_stories: None,
            can_promote_members: None,
            can_restrict_members: None,
            can_send_welcome_messages: None,
            is_anonymous: None,
        }),
    };
    let query = [("target", "groups")];
    let result = bot.default_admin_rights_set(&query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/default_admin_rights?target=groups" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"rights": {"can_delete_messages": true}}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": true
}
```

## menu_button.get <a href="#operation-menu-button-get" id="operation-menu-button-get"></a>

`GET /bot-api/v1/menu_button`

Read the configured menu button for your bot or one private chat. Omit `chat_id` to read the bot default.

A chat value of `default` uses the bot's default button. The bot's own default resolves to commands. See [Commands & profile](../commands-and-profile.md) to configure commands and menu behavior.

### Parameters <a href="#parameters-6" id="parameters-6"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | query | string | No | Optionally supply the ID of the private chat to select its menu button, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-6" id="request-6"></a>

Do not send a request body.

### Response <a href="#response-6" id="response-6"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [MenuButton](types/menubutton.md) in `result` |

### Errors <a href="#errors-6" id="errors-6"></a>

HTTP 400 `invalid_parameter` means the chat selection is invalid. HTTP 404 `chat_not_found` means the selected chat is missing or inaccessible.

### Example <a href="#example-6" id="example-6"></a>

Set `ARX_BOT_TOKEN` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
if (!arxBotToken) {
  throw new Error("Set ARX_BOT_TOKEN");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.menuButtonGet();
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.menu_button_get()
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.menu_button_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/menu_button" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "type": "commands"
  }
}
```

## menu_button.set <a href="#operation-menu-button-set" id="operation-menu-button-set"></a>

`PUT /bot-api/v1/menu_button`

Set the menu button for your bot or one private chat. Omit `chat_id` to set the bot default.

Use `type: default` for a chat to remove its override. Use `type: commands` to show the command menu. See [Commands & profile](../commands-and-profile.md) for the corresponding command lists.

### Parameters <a href="#parameters-7" id="parameters-7"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `chat_id` | query | string | No | Optionally supply the ID of the private chat to select its menu button, encoded as a decimal string. The value must match `^[1-9][0-9]*$`. |

### Request <a href="#request-7" id="request-7"></a>

Content type: `application/json`. You must include the body.

[MenuButton](types/menubutton.md)

The menu button configuration for your bot or one private chat. Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `type` | `"commands"` or `"default"` | Yes | The menu button kind. commands opens the command menu; default restores the default button. |

### Response <a href="#response-7" id="response-7"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-7" id="errors-7"></a>

HTTP 400 `invalid_parameter` means the menu type or chat selection is invalid. HTTP 404 `chat_not_found` means the selected chat is missing or inaccessible.

### Example <a href="#example-7" id="example-7"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.MenuButton = {
    "type": "commands"
  };
  const result = await bot.menuButtonSet({ body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.MenuButton(type='commands')
    result = bot.menu_button_set(body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::MenuButton {
        r#type: models::MenuButtonType::Commands,
    };
    let query = [];
    let result = bot.menu_button_set(&query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PUT \
  "https://signal.arx.pro/bot-api/v1/menu_button" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"type": "commands"}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": true
}
```

## profile.get <a href="#operation-profile-get" id="operation-profile-get"></a>

`GET /bot-api/v1/profile`

Read your bot's default profile text and its localizations.

Use `profile.update` to edit one language. A localization can inherit fields from the default profile. See [Commands & profile](../commands-and-profile.md) for profile text and language selection.

### Parameters <a href="#parameters-8" id="parameters-8"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `language_code` | query | string | No | Use a valid BCP 47 language tag. The response includes the default profile and all localizations regardless of this value. The maximum length is 32 characters. |

### Request <a href="#request-8" id="request-8"></a>

Do not send a request body.

### Response <a href="#response-8" id="response-8"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Profile](types/profile.md) in `result` |

### Errors <a href="#errors-8" id="errors-8"></a>

HTTP 400 `invalid_parameter` can identify an invalid `language_code`. Use a valid language tag of at most 32 characters, or omit it. Do not send a body or other query fields.

### Example <a href="#example-8" id="example-8"></a>

Set `ARX_BOT_TOKEN` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
if (!arxBotToken) {
  throw new Error("Set ARX_BOT_TOKEN");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.profileGet();
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.profile_get()
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [];
    let result = bot.profile_get(&query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/profile" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "default": {
      "description": "Acme Support",
      "name": "Acme Support",
      "short_description": ""
    },
    "localizations": {
      "en": {
        "description": "Acme Support",
        "name": null,
        "short_description": null
      }
    }
  }
}
```

## profile.update <a href="#operation-profile-update" id="operation-profile-update"></a>

`PATCH /bot-api/v1/profile`

Update your bot's profile text for one language. Include at least one text field.

Omitted fields keep their values. Use null or an empty string to clear an optional field or a localized name. Clearing a localization field removes its override. The default name must remain nonempty. Profile language tags can contain up to 32 characters.

The response returns the accepted language, text, and versions. See [Commands & profile](../commands-and-profile.md) for localization and text limits.

### Parameters <a href="#parameters-9" id="parameters-9"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-9" id="request-9"></a>

Content type: `application/json`. You must include the body.

[PatchProfile](types/patchprofile.md)

Change the default profile text or one localization. Omitted fields keep their values. Null or an empty string clears an optional field. You cannot clear the default name, but you can clear a localized name. Only the listed fields are allowed. If `language_code` is omitted or is `""`, `name` must have the type string and must match `\S`. Include at least one of `name`, `description`, or `short_description`.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `description` | string or null | No | This is the profile description for your bot. You can use up to 512 UTF-16 code units after markup is parsed. |
| `language_code` | string | No | This is the language tag for this profile or command set. An empty string selects the default. The maximum length is 32 characters. |
| `name` | string or null | No | This is the display name of the user, bot, contact, or provider. You can use up to 64 UTF-16 code units after markup is parsed. |
| `short_description` | string or null | No | This is the short profile description displayed for your bot. You can use up to 120 UTF-16 code units after markup is parsed. |

### Response <a href="#response-9" id="response-9"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [ProfileReceipt](types/profilereceipt.md) in `result` |

### Errors <a href="#errors-9" id="errors-9"></a>

HTTP 400 `invalid_parameter` identifies an invalid language or text field. Keep the default name nonempty and stay within the text limits.

### Example <a href="#example-9" id="example-9"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.PatchProfile = {
    "language_code": "en",
    "description": "Acme Support"
  };
  const result = await bot.profileUpdate({ body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.PatchProfile(language_code='en', description='Acme Support')
    result = bot.profile_update(body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::PatchProfile {
        description: arx_nexo::presence::Presence::Value("Acme Support".to_owned()),
        language_code: Some("en".to_owned()),
        name: arx_nexo::presence::Presence::Unset,
        short_description: arx_nexo::presence::Presence::Unset,
    };
    let query = [];
    let result = bot.profile_update(&query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X PATCH \
  "https://signal.arx.pro/bot-api/v1/profile" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{"language_code": "en", "description": "Acme Support"}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "config_version": "2",
    "description": "Acme Support",
    "language_code": "en",
    "name": null,
    "profile_version": "2",
    "short_description": null
  }
}
```

## profile.delete_photo <a href="#operation-profile-delete-photo" id="operation-profile-delete-photo"></a>

`DELETE /bot-api/v1/profile/photo`

Remove your bot's current avatar. Send the required empty JSON body `{}`.

This operation preserves profile text and localizations. Previously issued avatar references stop working after removal. Continue with [Commands & profile](../commands-and-profile.md) to manage the remaining profile fields.

### Parameters <a href="#parameters-10" id="parameters-10"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-10" id="request-10"></a>

Content type: `application/json`. You must include the body.

[Empty](types/empty.md)

The value has the type object.

An empty JSON object. Use `{}` when an operation requires a body but has no settings to send.

### Response <a href="#response-10" id="response-10"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-10" id="errors-10"></a>

HTTP 403 `owner_not_eligible` means the bot owner must resolve their account eligibility before changing the avatar.

### Example <a href="#example-10" id="example-10"></a>

Set `ARX_BOT_TOKEN`, `REQUEST_KEY` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.Empty = {};
  const result = await bot.profileDeletePhoto({ body, key: requestKey });
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

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.Empty(root={})
    result = bot.profile_delete_photo(body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = std::collections::BTreeMap::new();
    let query = [];
    let result = bot.profile_delete_photo(&query, Some(&body), Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X DELETE \
  "https://signal.arx.pro/bot-api/v1/profile/photo" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -H "Content-Type: application/json" \
  --data '{}'
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": true
}
```

## profile.set_photo <a href="#operation-profile-set-photo" id="operation-profile-set-photo"></a>

`PUT /bot-api/v1/profile/photo`

Replace your bot's avatar with a new photo or profile video.

Send multipart with one JSON `payload` part and one binary part. Use `type: static` with `photo.attach`, or `type: animated` with `animation.attach`. The attachment name must match the binary part. You cannot use library file IDs or URLs here.

Static images accept JPEG, PNG, or WebP up to 10 MiB. A profile video must be a square H.264 MP4, at most ten seconds and 10 MiB. If you set `main_frame_seconds`, use a nonnegative value below ten seconds and below the video's duration. See [Files](../files.md) for profile media requirements and upload failures.

### Parameters <a href="#parameters-11" id="parameters-11"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-11" id="request-11"></a>

Content type: `multipart/form-data`. You must include the body.

Send the avatar settings in the JSON `payload` part and one new binary part. Its name must match `photo.attach` or `animation.attach`. Library file IDs and URLs are not accepted. See [profile media](../files.md) for image and video requirements. Let your HTTP library set the multipart boundary.

The request deadline is 3,600 seconds.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `payload` | [SetProfilePhoto](types/setprofilephoto.md) | Yes | Send the JSON operation body in the payload part of a multipart request. |

### Response <a href="#response-11" id="response-11"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [Boolean](types/boolean.md) in `result` |

### Errors <a href="#errors-11" id="errors-11"></a>

HTTP 413 `file_too_large` means the avatar exceeds its size limit. HTTP 400 `file_type_mismatch` means the file bytes do not match the requested media kind. Check the profile media requirements before uploading again.

### Example <a href="#example-11" id="example-11"></a>

Set `ARX_BOT_TOKEN`, `FILE_PATH`, `REQUEST_KEY` before running this example. Set `FILE_PATH` to a PNG avatar you own.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models, UploadSource } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const filePath = process.env.FILE_PATH;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !filePath || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, FILE_PATH, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.SetProfilePhoto = {
    "type": "static",
    "photo": {
      "attach": "photo"
    }
  };
  const result = await bot.profileSetPhoto({ body, files: { "photo": UploadSource.fromPath(filePath, { contentType: "image/png" }) }, key: requestKey });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models, UploadSource

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.SetProfilePhoto(
        root=models.SetProfilePhotoVariant1(
            type='static',
            photo=models.SetProfilePhotoVariant1Photo(attach='photo'),
        ),
    )
    result = bot.profile_set_photo(body=body, files={'photo': UploadSource.from_path(os.environ["FILE_PATH"], content_type='image/png')}, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models, UploadSource, UploadPart };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let file_path = std::env::var("FILE_PATH")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::SetProfilePhoto::SetProfilePhotoVariant1(Box::new(models::SetProfilePhotoVariant1 {
        photo: models::SetProfilePhotoVariant1Photo {
            attach: "photo".to_owned(),
        },
        r#type: models::SetProfilePhotoVariant1Type::Static,
    }));
    let result = bot.profile_set_photo(&body, vec![UploadPart::new("photo", UploadSource::from_path(&file_path)?.with_content_type("image/png")?)?], Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 3600 -X PUT \
  "https://signal.arx.pro/bot-api/v1/profile/photo" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -F 'payload={"type": "static", "photo": {"attach": "photo"}};type=application/json' \
  -F "photo=@${FILE_PATH};type=image/png"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": true
}
```

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
