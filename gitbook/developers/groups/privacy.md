---
description: "Understand group privacy, which group messages reach your bot and why, how commands are routed, and how to send to a group or read its history."
---

# What your bot receives in groups

In a private chat, your bot receives every message the user sends. In a Supergroup, it receives either every message or only the messages addressed to it. This choice protects members' conversations from bots that don't need them, and it is the most common reason a group bot seems to miss messages.

## Two access levels <a href="#two-access-levels" id="two-access-levels"></a>

| Access | When it applies | What your bot receives |
| --- | --- | --- |
| `all_messages` | Your bot is an administrator of the group, or group privacy is off for your bot. | Every message members send, plus everything below. |
| `addressed_only` | Group privacy is on (the default) and your bot is not an administrator. | Only the messages listed in [Messages addressed to your bot](#messages-addressed-to-your-bot). |

Group privacy is a bot-wide setting. Turn it off with `/setprivacy` in [NexoCreator](../nexocreator.md); `me.get` then returns `settings.can_read_all_group_messages` as `true`. Making your bot an administrator gives it `all_messages` in that group regardless of the setting.

A group can require bots to be administrators before they read every message. In such a group, turning privacy off has no effect; your bot keeps `addressed_only` until an administrator promotes it.

Messenger shows people the access level before they add a bot: "Reads all messages in the group" or "Reads only commands and replies addressed to it". Ask for `all_messages` only when your bot needs it, for example to moderate content or summarize discussions.

### Privacy changes <a href="#privacy-changes" id="privacy-changes"></a>

When your bot's effective access changes, every active bot in the group, including yours, receives a `message` update with a service message:

```json
{
  "kind": "service",
  "service": {
    "type": "bot_privacy_changed",
    "bot": { "id": "10000042", "is_bot": true, "name": "Acme Support", "username": "ACMESUPPORTBOT" },
    "access": "all_messages"
  }
}
```

`access` is the new effective level for the bot named in `bot`. The change applies to messages accepted after it. Messages that were already routed are not delivered again.

## Messages addressed to your bot <a href="#messages-addressed-to-your-bot" id="messages-addressed-to-your-bot"></a>

With `addressed_only`, a member's message reaches your bot when one of these applies:

| The message | Example |
| --- | --- |
| Replies to a message your bot sent | A reply to your bot's question. |
| Replies to a message that was addressed to your bot | A reply to a member's `/report@ACMESUPPORTBOT`. |
| Is a command with your bot's handle | `/status@ACMESUPPORTBOT` |
| Is a command without a handle, and your bot was the last bot to post in the group | `/status` |
| Mentions your bot's handle | `Hi @ACMESUPPORTBOT, can you help?` |
| Was sent through your bot (`via_bot`) | Reserved for inline results. Inline mode isn't available for bots yet. |
| Edits a message your bot received | Arrives as `edited_message`. |

A mention reaches your bot in addition to any other target, but it doesn't start a reply chain: a reply to a message that only mentioned your bot is not addressed to it.

These reach every bot in the group, whatever its access level:

| Update | Why |
| --- | --- |
| Service messages, such as `members_joined`, `member_left`, `title_changed`, `photo_changed`, `message_pinned` and topic events | Group state changes that any bot may need. The creation message is never delivered. |
| `callback_query` for your bot's own inline buttons | Presses on your buttons are always addressed to your bot. |
| `my_chat_member` | Your own membership. |

When a message your bot receives is a reply, its `reply_to` includes one level of the quoted message, even if your bot never received that message. Nested quotes are omitted.

### Messages your bot doesn't receive <a href="#messages-your-bot-doesnt-receive" id="messages-your-bot-doesnt-receive"></a>

- Messages that other bots send. By default each bot's messages are isolated from other bots, and [`messages.history`](#read-group-history) omits them.
- Your bot's own messages.
- Automatic forwards from a linked channel, unless your bot has `all_messages`.
- Messages from before your bot joined. Read them with [`messages.history`](#read-group-history) when your bot has `all_messages`.

### Senders <a href="#senders" id="senders"></a>

`sender_user` identifies the person who sent a message. When an anonymous administrator posts as the group, or a linked channel forwards a post, the message has `sender_chat` with the group or channel `id`, `kind` and `title`, and no `sender_user`. Don't treat `sender_chat` as a person. `sender_tag` is the member tag shown next to the sender.

## Commands in groups <a href="#commands-in-groups" id="commands-in-groups"></a>

People see your command menu in groups too. Configure which commands they see with the group scopes of [`commands.set`](../commands-and-profile.md#scope-and-language):

| Scope | Who sees the list | Required query fields |
| --- | --- | --- |
| `all_group_chats` | Every member of every group. | None |
| `all_chat_administrators` | Administrators of every group. | None |
| `chat` | Every member of one group. | `chat_id` |
| `chat_administrators` | Administrators of one group. | `chat_id` |
| `chat_member` | One member of one group. | `chat_id`, `user_id` |

For a group member, ARX checks `chat_member`, `chat`, `all_group_chats` and then `default`. For an administrator, it checks `chat_member`, `chat_administrators`, `chat`, `all_chat_administrators`, `all_group_chats` and then `default`. Administrator scopes apply only while the person is an administrator. At each scope ARX tries the person's language before the fallback list; the first configured list wins. The `chat` scopes require your bot to be a current member of that group, and `chat_member` requires the user to be a member.

A command list changes what people see, not what they can type. Check the sender's status with [`members.get`](admin-rights.md#read-members) before running an administrator command.

This example shows `/help` to everyone in groups and adds `/ban` and `/mute` for administrators.

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
const help = { command: "help", description: "Show what this bot can do" };
try {
  await bot.commandsSet({
    query: { scope: "all_group_chats" },
    key: `${key}-members`,
    body: { commands: [help] },
  });
  await bot.commandsSet({
    query: { scope: "all_chat_administrators" },
    key: `${key}-admins`,
    body: {
      commands: [
        help,
        { command: "ban", description: "Reply to a message to ban its sender" },
        { command: "mute", description: "Reply to a message to mute its sender for one hour" },
      ],
    },
  });
} finally {
  await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models

key = os.environ["REQUEST_KEY"]
help_command = models.Command(command="help", description="Show what this bot can do")
with Client(
    Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")
) as bot:
    bot.commands_set(
        query={"scope": "all_group_chats"},
        body=models.SetCommands(commands=[help_command]),
        key=f"{key}-members",
    )
    bot.commands_set(
        query={"scope": "all_chat_administrators"},
        body=models.SetCommands(
            commands=[
                help_command,
                models.Command(command="ban", description="Reply to a message to ban its sender"),
                models.Command(
                    command="mute",
                    description="Reply to a message to mute its sender for one hour",
                ),
            ]
        ),
        key=f"{key}-admins",
    )
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::models::{Command, SetCommands};
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let help = Command::new("help".into(), "Show what this bot can do".into());
    let members = SetCommands::new(vec![help.clone()]);
    bot.commands_set(&[("scope", "all_group_chats")], Some(&members), Some(&format!("{key}-members")))
        .await?;
    let admins = SetCommands::new(vec![
        help,
        Command::new("ban".into(), "Reply to a message to ban its sender".into()),
        Command::new("mute".into(), "Reply to a message to mute its sender for one hour".into()),
    ]);
    bot.commands_set(
        &[("scope", "all_chat_administrators")],
        Some(&admins),
        Some(&format!("{key}-admins")),
    )
    .await?;
    Ok(())
}
```

{% endtab %}
{% endtabs %}

## Send to a group <a href="#send-to-a-group" id="send-to-a-group"></a>

Use the group's chat ID with the same methods you use in private chats: `messages.send`, `messages.send_album`, `messages.copy`, `messages.forward` and `messages.edit`. Your bot edits only its own messages. Add an inline keyboard as usual; presses arrive as `callback_query` updates from any member who presses the button, so check `callback_query.user` before acting. See [Send messages](../messages.md) and [Keyboards](../keyboards.md).

| Field | Use in a group |
| --- | --- |
| `reply_to.message_id` | Reply to a member's message. In a forum, also send the message's `topic_id`. |
| `topic_id` | Post in a forum topic. Omit it to post in General. Sending it to a group without topics fails with `forum_disabled`. See [Forum topics](topics.md). |
| `silent` | Send without a notification sound. |
| `protect_content` | Prevent members from copying and forwarding the message. |

A bot member follows the group's default permissions, like any member. A bot administrator isn't limited by them. These errors are specific to groups:

| Code | HTTP | Cause and fix |
| --- | --- | --- |
| `send_permission_denied` | 403 | The group doesn't allow this content type for members. `details.permission` names it, such as `send_photos` or `send_polls`. Send another type or ask for administrator status. |
| `topic_closed` | 403 | The topic is closed. Administrators with `can_manage_topics` can still post. |
| `topic_not_found` | 404 | The topic doesn't exist or was deleted. |
| `forum_disabled` | 409 | The group has no topics. Omit `topic_id`. |
| `protected_content` | 403 | You tried to copy or forward from a group with protected content. |
| `chat_not_found` | 404 | Your bot is not a current member, or the group doesn't exist. |
| `group_moderated` | 403 | The group is restricted by ARX moderation. |
| `group_deleting` | 410 | The group is being deleted. Stop sending to it. |

## Read group history <a href="#read-group-history" id="read-group-history"></a>

`messages.history` returns up to `limit` (1 to 100, default 50) messages of a group in ascending `message_id` order, in the same shape as `message` updates. `messages.get` returns up to 100 specific messages by ID, for example the original of a reply.

Your bot needs `all_messages` access: group privacy off, or administrator. Otherwise both methods fail with `history_access_required` (HTTP 403). Your bot reads what a member who joined when it did can see: messages since it joined, or the full visible history if the group showed history to new members at that time. Messages from other bots, deleted, expired and ephemeral messages are omitted. Edited messages appear in their latest revision.

| Query field | Rule |
| --- | --- |
| `limit` | 1 to 100. A page can contain fewer messages; continue while `has_more` is `true`. |
| `before_message_id` | Return messages older than this ID. Continue with `next_before_message_id`. |
| `after_message_id` | Return messages newer than this ID. Continue with `next_after_message_id`. Don't combine it with `before_message_id`. |
| `topic_id` | Read one forum topic. `1` is General. Omit it to read every topic. |

This example prints the newest 20 messages of a group.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const chatId = process.env.CHAT_ID;
if (!token || !chatId) {
  throw new Error("Set ARX_BOT_TOKEN, CHAT_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const page = await bot.messagesHistory(chatId, { query: { limit: 20 } });
  for (const message of page.messages) {
    console.log(message.message_id, message.fallback_text ?? `(${message.kind})`);
  }
  if (page.has_more) {
    console.log(`Older messages: before_message_id=${page.next_before_message_id}`);
  }
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
    Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")
) as bot:
    page = bot.messages_history(os.environ["CHAT_ID"], query={"limit": 20})
    for message in page.messages:
        print(message.message_id, message.fallback_text or f"({message.kind})")
    if page.has_more:
        print(f"Older messages: before_message_id={page.next_before_message_id}")
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::models::GroupMessage;
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let page = bot.messages_history(&chat_id, &[("limit", "20")], None, None).await?;
    for message in &page.messages {
        let (id, text) = match message {
            GroupMessage::GroupContentMessage(m) => (&m.message_id, m.fallback_text.as_deref()),
            GroupMessage::GroupServiceMessage(m) => (&m.message_id, m.fallback_text.as_deref()),
        };
        println!("{id} {}", text.unwrap_or("(no text)"));
    }
    if page.has_more {
        println!("Older messages: before_message_id={:?}", page.next_before_message_id);
    }
    Ok(())
}
```

{% endtab %}
{% endtabs %}

See [`messages.history`](../reference/messages.md#operation-messages-history) and [`messages.get`](../reference/messages.md#operation-messages-get).
