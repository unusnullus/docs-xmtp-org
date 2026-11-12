---
description: "Connect Claude Code, Claude Desktop, Cursor, VS Code or any MCP client to the ARX Nexo MCP server and let your agent build, configure and test your bot."
---

# Build with an AI agent (MCP)

The ARX Nexo MCP server lets an AI agent work on your bot for you. Your agent connects to it with your bot token and gets a tool for each Bot API operation it can run with a bot token, plus guides, documentation search, project generation and health checks. You describe the bot you want; the agent configures it, writes the code with an official SDK and tests it with you.

The server operates your bot through the public Bot API with your token, exactly as your own program would. It does not host your code: you run the program the agent generates, on your machine or your server.

## What the agent can do <a href="#what-the-agent-can-do" id="what-the-agent-can-do"></a>

- Read and set your bot's profile, commands, menu button and suggested administrator rights.
- Send, edit, copy, forward, pin and delete every message type: text, rich text, photos, videos, files, albums, polls, checklists, dice, locations, venues, contacts, invoices, keyboards, effects and drafts.
- Read updates, set, inspect, test and delete your webhook.
- Work in Supergroups and forums: topics, members, administrator rights, permissions, invite links, join requests and moderation.
- Open Mini Apps from buttons and the menu button, and answer Mini App queries.
- Create invoices, answer checkouts, manage subscriptions, refund charges and read payment reports.
- Manage connected Business accounts' profiles and the access settings of bots your bot manages.
- Generate a working bot project in TypeScript, Python or Rust.
- Check your bot's health and send a test message.

Some steps need you in NexoCreator because they use your ARX account: creating the bot and reading its token, registering Mini App origins, choosing the wallet that receives payments, and turning on advanced modes. The agent tells you exactly what to do there and what to bring back. See [Steps you take in NexoCreator](#steps-you-take-in-nexocreator).

## Connect your agent <a href="#connect-your-agent" id="connect-your-agent"></a>

The server address is:

```text
https://signal.arx.pro/bot-api/v1/mcp
```

It uses MCP Streamable HTTP. Every request carries your bot token in the `Authorization` header as `Bearer <BOT_TOKEN>`. Create the bot and save its token with the [quickstart](quickstart.md#create-the-bot). If you don't have a bot yet, connect without the header first: the guide tools walk you through creating it. Store the token in an environment variable such as `ARX_BOT_TOKEN`. Keep it out of files you commit and never paste it into the chat with your agent.

{% tabs %}
{% tab title="Claude Code" %}

```sh
claude mcp add --transport http arx-nexo 'https://signal.arx.pro/bot-api/v1/mcp' \
  --header "Authorization: Bearer $ARX_BOT_TOKEN"
```

Run `/mcp` in Claude Code to confirm that `arx-nexo` is connected.

{% endtab %}
{% tab title="Claude Desktop" %}

Open **Settings**, then **Developer**, then **Edit Config**, and add the server to `claude_desktop_config.json`. The `mcp-remote` bridge forwards the header and needs Node.js.

```json
{
  "mcpServers": {
    "arx-nexo": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://signal.arx.pro/bot-api/v1/mcp",
        "--header",
        "Authorization:${ARX_AUTH_HEADER}"
      ],
      "env": {
        "ARX_AUTH_HEADER": "Bearer <BOT_TOKEN>"
      }
    }
  }
}
```

Replace `<BOT_TOKEN>` with your token, save the file and restart Claude Desktop.

{% endtab %}
{% tab title="Cursor" %}

Add the server to `~/.cursor/mcp.json`, or to `.cursor/mcp.json` in your project:

```json
{
  "mcpServers": {
    "arx-nexo": {
      "url": "https://signal.arx.pro/bot-api/v1/mcp",
      "headers": {
        "Authorization": "Bearer ${env:ARX_BOT_TOKEN}"
      }
    }
  }
}
```

Start Cursor from a terminal where `ARX_BOT_TOKEN` is set.

{% endtab %}
{% tab title="VS Code" %}

Add the server to `.vscode/mcp.json`. VS Code asks for the token once and stores it securely.

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "arx-bot-token",
      "description": "ARX bot token",
      "password": true
    }
  ],
  "servers": {
    "arx-nexo": {
      "type": "http",
      "url": "https://signal.arx.pro/bot-api/v1/mcp",
      "headers": {
        "Authorization": "Bearer ${input:arx-bot-token}"
      }
    }
  }
}
```

{% endtab %}
{% tab title="Other clients" %}

Configure a remote MCP server with:

| Setting | Value |
| --- | --- |
| Transport | Streamable HTTP |
| URL | `https://signal.arx.pro/bot-api/v1/mcp` |
| Header | `Authorization: Bearer <BOT_TOKEN>` |

The server is stateless. It answers each POST in the same HTTP response, issues no session ID and does not open event streams. Requests from a browser (with an `Origin` header) are refused with HTTP 403; use a desktop, CLI or server-side client. Clients on protocol version 2025-03-26 may batch up to 16 messages per POST; later versions send one message per POST.

{% endtab %}
{% endtabs %}

You can connect before you have a bot. Without a token, the agent sees only the guide tools and can walk you through creating the bot. After you add the token and reconnect, it sees every bot tool.

## Authentication and security <a href="#authentication-and-security" id="authentication-and-security"></a>

- One connection works with one bot: the bot whose token you send. To work on another bot, add a second server entry with its token.
- The server does not store or log your token, and it removes token-shaped text from every result.
- A rejected token returns the `unauthorized` error. If it leaked, rotate it in NexoCreator and update your client. See [Authentication & tokens](authentication.md).
- Tools that delete, ban, restrict, refund or revoke carry the MCP destructive hint, so clients that honor it ask before running them. Read each request before you approve it.
- Tools that read or reveal another bot's token, and end-to-end encrypted Business operations, are not available through the server. Your own program does those with the SDK.

## Limits <a href="#limits" id="limits"></a>

| Limit | Behavior |
| --- | --- |
| Calls per bot | The server limits tool calls per bot token per minute. A refused call says when to retry. The [Bot API limits](limits.md) still apply. |
| Uploads | Files sent through a tool are base64 encoded. Their decoded total is limited to 2 MiB per call, and the whole MCP request to 4 MiB. For larger media, use a public `url` source or upload from your program. |
| Downloads | A tool reads at most 4 MiB from the Bot API, so `files_download` returns files up to 4 MiB. Download larger files from your program. |
| Request time | Each request must finish within 75 seconds. |
| Polling | `updates_get` waits at most 25 seconds. |

## Steps you take in NexoCreator <a href="#steps-you-take-in-nexocreator" id="steps-you-take-in-nexocreator"></a>

Your agent calls `creator_steps` to get the exact instructions for each of these and to know what to ask you to bring back.

| Task | What you do in `@NEXOCREATOR` | What the agent needs from you |
| --- | --- | --- |
| Create the bot | `/newbot` or `/newtestbot`, then **View token** | The handle; the token goes into your client configuration |
| Rotate the token | `/token`, then **Rotate token** | Confirmation that every program and the client use the new token |
| Add testers | `/testers` for a test bot | The testers' handles |
| Group behavior | `/setjoingroups`, `/setprivacy`, `/setjoinrequests` | Your choices and a test group |
| Mini Apps | `/setapporigins`, `/setmainapp` | The HTTPS origin and Main Mini App URL |
| Payments | **Payments**: wallet, settings, plans and requirements | Confirmation that the checklist is complete |
| Advanced modes | **Advanced modes**: review and accept the terms | Which modes are on |
| Delivery | `/delivery`: pause, resume or drop updates | The action you confirmed |

The agent then verifies each step with a tool, for example `payment_readiness_get` after you set up payments. See [NexoCreator](nexocreator.md) for every command.

## Tools <a href="#tools" id="tools"></a>

The bot tools follow the [API reference](api.md). Their names are the operation names with underscores, such as `messages_send` for `messages.send`. Each tool lists its parameters and body fields, names the body examples it has, and says whether it reads, writes or destroys data.

### Guide tools <a href="#guide-tools" id="guide-tools"></a>

The guide tools through `bot_status` work without a token. `bot_roundtrip_test` and `bot_webhook_check` require a bot token.

| Tool | Use |
| --- | --- |
| `nexo_guide` | Step-by-step guides: start, creator, connect, messages, keyboards, updates, groups, mini_apps, payments, business, testing, errors. |
| `creator_steps` | What to ask you to do in NexoCreator, and what to bring back. |
| `docs_search` | Search the guides and every bot tool. |
| `api_operation` | The full contract of one tool, or one complete example body. |
| `api_schema` | One named schema from the API contract. |
| `scaffold_project` | A bot project for the TypeScript, Python or Rust SDK, with polling or a webhook receiver. |
| `bot_status` | Identity, delivery mode, webhook state, pending updates, recent errors, commands, menu button, payment setup and next steps. |
| `bot_roundtrip_test` | Sends a test message and reports how long the Bot API took to accept it. |
| `bot_webhook_check` | Sends a signed test ping to your webhook and reports the result. |

The server also offers its guides and the API contract as MCP resources, and the prompts `build_a_bot` and `diagnose_bot`.

### Bot tools <a href="#bot-tools" id="bot-tools"></a>

| Toolset | Tools |
| --- | --- |
| `bot` | `me_get`, `token_revoke` |
| `profile` | `profile_get`, `profile_update`, `profile_set_photo`, `profile_delete_photo`, `commands_get`, `commands_set`, `commands_delete`, `commands_resolve`, `menu_button_get`, `menu_button_set`, `default_admin_rights_get`, `default_admin_rights_set` |
| `messages` | `messages_send`, `messages_send_album`, `messages_edit`, `messages_copy`, `messages_forward`, `messages_delete`, `messages_delete_batch`, `messages_get`, `messages_history`, `mini_app_queries_answer`, `prepared_messages_save` |
| `polls_and_locations` | `messages_stop_poll`, `messages_delete_poll_option`, `messages_stop_live_location` |
| `reactions_and_pins` | `reactions_set`, `messages_pin`, `messages_unpin`, `messages_unpin_all` |
| `drafts_and_actions` | `drafts_set`, `drafts_delete`, `chats_send_action` |
| `keyboards` | `callback_queries_answer`, `prepared_buttons_save` |
| `files` | `files_upload`, `files_get`, `files_download` |
| `updates_and_webhooks` | `updates_get`, `webhook_set`, `webhook_get`, `webhook_test`, `webhook_delete` |
| `chats_and_users` | `chats_get`, `chats_resolve`, `users_get_chat`, `users_list_profile_photos`, `users_list_personal_channel_posts` |
| `group_moderation` | `chats_set_permissions`, `chats_set_photo`, `chats_delete_photo`, `chats_leave`, `members_get`, `members_count`, `members_list_admins`, `members_promote`, `members_restrict`, `members_ban`, `members_unban`, `members_set_custom_title`, `sender_chats_ban`, `sender_chats_unban`, `invite_links_create`, `invite_links_edit`, `invite_links_export`, `invite_links_revoke`, `join_requests_approve`, `join_requests_decline` |
| `topics` | `topics_list`, `topics_get`, `topics_create`, `topics_edit`, `topics_close`, `topics_reopen`, `topics_delete`, `topics_unpin_all`, `topics_list_icons` |
| `join_requests` | `join_request_queries_answer`, `join_request_queries_open_mini_app` |
| `payments` | `payment_readiness_get`, `payment_tokens_list`, `payment_plans_list`, `invoice_links_create`, `pre_checkout_queries_answer`, `entitlements_get`, `subscriptions_list`, `subscriptions_get`, `subscriptions_update`, `subscriptions_extend`, `charges_refund`, `merchant_balance_get`, `merchant_transactions_list`, `payment_analytics_get` |
| `business_account` | `business_connections_get`, `business_endpoint_get`, `business_account_bio_set`, `business_account_username_set`, `business_account_set_photo`, `business_account_delete_photo` |
| `managed_bots` | `managed_bots_get_access`, `managed_bots_set_access` |
| `guest_queries` | `guest_queries_answer` |
| `platform` | `platform_get`, `message_effects_list` |

These public operations have no tool: `inline_queries.answer`, because inline mode cannot be turned on for bots yet; the end-to-end encrypted Business operations (`business_envelopes.send`, `business_endpoint.register`, `business_endpoint.upload_prekeys`, `business_endpoint.reset`, `business_owner_devices.list`, `business_peer_devices.list`, `business_connection.endpoint_report.get` and `.set`), because they need your encrypting endpoint's own keys or credentials; `managed_bots.get_token` and `managed_bots.rotate_token`, because bot tokens never pass through the server; and `openapi.get`, because the contract is available as the `nexo://contract/openapi.json` resource and through `api_operation` and `api_schema`. `subscriptions_extend`, `merchant_transactions_list` and `payment_analytics_get` exist as tools but fail until those operations are [available](not-available-yet.md#payments).

### Choose toolsets <a href="#choose-toolsets" id="choose-toolsets"></a>

Some clients limit how many tools they load. Add `toolsets` to the server URL with a comma-separated list of toolset names to load only those bot tools. The guide tools are always loaded.

```text
https://signal.arx.pro/bot-api/v1/mcp?toolsets=profile,messages,keyboards,updates_and_webhooks
```

## Example session <a href="#example-session" id="example-session"></a>

This session builds a support bot from zero. You type the requests; the agent calls the tools.

1. You ask: "Build a support bot for Acme in Python that answers /help."
2. The agent calls `nexo_guide`. It sees no bot tools, so it calls `creator_steps` with `create_bot` and tells you to send `/newbot` to `@NEXOCREATOR`, name the bot **Acme Support** with the handle `ACMESUPPORTBOT`, and save the token.
3. You save the token, add it to your client as shown in [Connect your agent](#connect-your-agent) and reconnect.
4. The agent calls `bot_status`. It reports the handle, polling delivery, no commands and no pending updates.
5. The agent calls `profile_update` with a description and short description, then `commands_set` with `start` and `help`.
6. The agent calls `scaffold_project` with `python` and `polling`, writes the files into `acme-support/`, and tells you to install the SDK and run `python bot.py` with `ARX_BOT_TOKEN` and `ARX_BOT_API_BASE_URL` set.
7. You open the bot in ARX Messenger, choose **Start** and send `/help`. The bot answers.
8. The agent calls `bot_roundtrip_test`. It sends a test message to your chat and reports the time the Bot API took to accept it. You confirm the message arrived.
9. You ask for a button that opens your help center. The agent reads the body example `inline_keyboard` with `api_operation`, changes the handler to send a `url` button, and you restart the program.

To move to a webhook later, ask the agent to generate the webhook variant. After you deploy the receiver on a public HTTPS URL, it calls `webhook_set` and `bot_webhook_check`.

## Troubleshooting <a href="#troubleshooting" id="troubleshooting"></a>

| Symptom | What to do |
| --- | --- |
| Only guide tools are listed | The client sends no `Authorization` header, or its value isn't `Bearer <bot ID>:aerx_<secret>`. Check the header and reconnect. `bot_status` explains what is wrong. |
| `unauthorized` | The token is wrong, rotated or revoked. Update the header with the current token. |
| `webhook_active` from `updates_get` | Your bot uses a webhook. Use `webhook_get`, or `webhook_delete` to return to polling. |
| `chat_not_started` | The user must open your bot and choose **Start** first. |
| `capability_not_available` | The feature is off for your bot. Check `bot_status` and NexoCreator. |

Every failed tool call includes the error code, what it means and the next step. See [Errors](errors.md) for the full list.
