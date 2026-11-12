---
description: "Add your bot to Supergroups, choose which messages it receives and reply in the right conversation and topic."
---

# Bots in groups

Your bot can work in ARX Supergroups. People add it as a member or as an administrator. Once it is in a group, it receives the messages addressed to it, replies in the group or in a forum topic, and, with the rights an administrator grants, moderates members, manages invite links and answers join requests.

## Where your bot can work <a href="#where-your-bot-can-work" id="where-your-bot-can-work"></a>

| Conversation | Your bot can join | Notes |
| --- | --- | --- |
| Private chat | Yes, after the user starts it | See [Create your first bot](quickstart.md). |
| Supergroup | Yes | The group type for large communities, with administrators, topics and bots. |
| Forum | Yes | A Supergroup with topics. See [Forum topics](groups/topics.md). |
| Supergroup in a community | Yes | It behaves as any other Supergroup. See [Communities](#communities). |
| Group (encrypted) | No | Messenger's end-to-end encrypted groups don't accept bots. |

In an update, `chat.kind` is `group` for a Supergroup and `private` for a private chat. A group's chat ID is a decimal string, like every chat ID, and is never a user ID. Store it as a string.

## Prepare your bot <a href="#prepare-your-bot" id="prepare-your-bot"></a>

Configure group behavior in [NexoCreator](nexocreator.md). Each command asks you to select a bot.

| NexoCreator command | Default | What it controls |
| --- | --- | --- |
| `/setjoingroups` | On | Whether people can add your bot to groups. When it is off, adding the bot fails with `bot_cannot_join_groups`. |
| `/setprivacy` | On | Group privacy. On: your bot receives only commands, mentions and replies addressed to it. Off: it receives every message. See [What your bot receives](groups/privacy.md). |
| `/setdefaultadminrights` | No rights | The administrator rights your bot suggests when someone adds it as an administrator. |
| `/setjoinrequests` | Off | Whether your bot can act as a join request guard. See [Join requests](groups/invites-and-requests.md#guard-join-requests). |

`me.get` returns the current values in `settings`: `can_join_groups`, `can_read_all_group_messages` (privacy off) and `supports_join_request_queries`. `features.nexo_groups` is `available` when your bot can join groups.

## Handle your first group <a href="#handle-your-first-group" id="handle-your-first-group"></a>

This program replies to `/help` in groups. It answers in the same topic, as a reply to the command, and logs when someone adds or removes the bot. Set `ARX_BOT_TOKEN`, add the bot to a test Supergroup, and send `/help` there.

With privacy on, the bot receives `/help@YOURBOT` and also a plain `/help` when it was the last bot to post in the group. See [Commands in groups](groups/privacy.md#commands-in-groups).

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
if (!token) {
  throw new Error("Set ARX_BOT_TOKEN");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
const me = await bot.meGet();
let offset: string | undefined;

function isCommand(text: string, name: string): boolean {
  const [command, target] = text.split(/\s/, 1)[0].split("@");
  return command === `/${name}` && (!target || target.toUpperCase() === me.username);
}

try {
  while (true) {
    const batch = await bot.updatesGet({
      query: { timeout_seconds: 30, ...(offset ? { offset } : {}) },
    });
    for (const update of batch.updates) {
      const change = update.type === "my_chat_member" ? update.my_chat_member : undefined;
      if (change && "chat" in change && change.chat) {
        console.log(`${change.chat.id}: ${change.old.status} -> ${change.new.status}`);
      }
      const message = update.type === "message" ? update.message : undefined;
      if (message) {
        if (
          message.chat.kind === "group" &&
          message.kind === "content" &&
          message.content.type === "text" &&
          isCommand(message.content.text, "help")
        ) {
          await bot.messagesSend(message.chat.id, {
            key: `help-${update.update_id}`,
            body: {
              content: { type: "text", text: "Send /help@" + me.username + " to reach me." },
              reply_to: { message_id: message.message_id },
              ...(message.topic_id ? { topic_id: message.topic_id } : {}),
            },
          });
        }
      }
      offset = (BigInt(update.update_id) + 1n).toString();
    }
  }
} finally {
  await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, FormattedText, models

def is_command(text: str, name: str, username: str) -> bool:
    command, _, target = text.split(maxsplit=1)[0].partition("@")
    return command == f"/{name}" and (not target or target.upper() == username)

with Client(
    Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")
) as bot:
    me = bot.me_get()
    offset: str | None = None
    while True:
        batch = bot.updates_get(offset=offset, timeout_seconds=30)
        for update in batch.updates:
            change = update.my_chat_member
            if change is not None and change.chat is not None:
                print(f"{change.chat.id}: {change.old.status} -> {change.new.status}")
            message = update.message
            if (
                message is not None
                and message.chat.kind == "group"
                and message.content is not None
                and message.content.type == "text"
                and is_command(message.content.text or "", "help", me.username)
            ):
                text = FormattedText(f"Send /help@{me.username} to reach me.").content()
                reply_to = models.InputReply(message_id=message.message_id)
                request = (
                    models.SendMessage(
                        content=text, reply_to=reply_to, topic_id=message.topic_id
                    )
                    if message.topic_id
                    else models.SendMessage(content=text, reply_to=reply_to)
                )
                bot.messages_send(
                    message.chat.id, body=request, key=f"help-{update.update_id}"
                )
            offset = str(int(update.update_id) + 1)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::models::{
    Content, GroupMessage, IncomingMemberUpdated, IncomingMessage, InputReply, SendMessage, Update,
};
use arx_nexo::{Client, Config, FormattedText};

fn is_command(text: &str, name: &str, username: &str) -> bool {
    let first = text.split_whitespace().next().unwrap_or("");
    let (command, target) = first.split_once('@').unwrap_or((first, ""));
    command == format!("/{name}") && (target.is_empty() || target.eq_ignore_ascii_case(username))
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let me = bot.me_get(&[], None, None).await?;
    let mut offset: Option<String> = None;
    loop {
        let mut query = vec![("timeout_seconds", "30")];
        if let Some(value) = offset.as_deref() {
            query.push(("offset", value));
        }
        let batch = bot.updates_get(&query, None, None).await?;
        for update in batch.updates {
            if let Update::UpdateMyChatMember(item) = &update {
                if let IncomingMemberUpdated::GroupMemberUpdated(change) = &item.my_chat_member {
                    println!("{}: {:?} -> {:?}", change.chat.id, change.old, change.new);
                }
            }
            if let Some(IncomingMessage::GroupMessage(group)) = update.message() {
                if let GroupMessage::GroupContentMessage(message) = group.as_ref() {
                    if let Content::Text(text) = &message.content {
                        if is_command(&text.text, "help", &me.username) {
                            let reply = format!("Send /help@{} to reach me.", me.username);
                            let request = SendMessage {
                                reply_to: Some(InputReply::new(message.message_id.clone())),
                                topic_id: message.topic_id.clone(),
                                ..SendMessage::new(FormattedText::new(&reply, vec![])?.content()?)
                            };
                            let key = format!("help-{}", update.update_id());
                            bot.messages_send(&message.chat.id, &[], Some(&request), Some(&key))
                                .await?;
                        }
                    }
                }
            }
            let next = update.update_id().parse::<u64>()?.checked_add(1).ok_or("overflow")?;
            offset = Some(next.to_string());
        }
    }
}
```

{% endtab %}
{% endtabs %}

The program keeps its offset in memory. Before deploying, follow [Polling and webhooks](updates.md) for durable processing.

## What your bot can do in a group <a href="#what-your-bot-can-do-in-a-group" id="what-your-bot-can-do-in-a-group"></a>

| Task | Requirement | Guide |
| --- | --- | --- |
| Receive messages addressed to it | Membership | [What your bot receives](groups/privacy.md) |
| Receive every message, read history | Privacy off, or administrator | [What your bot receives](groups/privacy.md#read-group-history) |
| Send, edit and delete its own messages | Membership and the group's send permissions | [Send to a group](groups/privacy.md#send-to-a-group) |
| Post in a forum topic | Membership | [Forum topics](groups/topics.md) |
| Create topics; edit, close and reopen any topic | `can_manage_topics` (topics your bot created need no right) | [Forum topics](groups/topics.md#manage-topics) |
| List administrators, count members | Membership | [Administrator rights](groups/admin-rights.md#read-members) |
| Read another member's status | Administrator | [Administrator rights](groups/admin-rights.md#read-members) |
| Ban, restrict and remove members | `can_restrict_members` | [Moderation](groups/moderation.md) |
| Delete other members' messages | `can_delete_messages` | [Moderation](groups/moderation.md#delete-messages) |
| Pin messages | `can_pin_messages` | [Moderation](groups/moderation.md#pin-messages) |
| Set or delete the group photo | `can_change_info` | [Administrator rights](groups/admin-rights.md#change-group-information) |
| Promote members | `can_promote_members` | [Administrator rights](groups/admin-rights.md#promote-a-member) |
| Create invite links, approve join requests | `can_invite_users` | [Invite links and join requests](groups/invites-and-requests.md) |

Group administration operations return `capability_not_available` (HTTP 403) when the feature is not enabled for your bot. Handle that error as an unavailable feature, not as a missing right.

## Group limits <a href="#group-limits" id="group-limits"></a>

| Limit | Value | Error |
| --- | --- | --- |
| Bots in one group | 20 | `group_bots_limit_reached` (HTTP 409) |
| Owner and administrators in one group, bots included | 50 | `group_admins_limit_reached` (HTTP 409) |
| Pinned messages in one group | 1,000 | `pin_limit_reached` (HTTP 409) |
| Active invite links your bot created in one group | 1,000 | `invite_link_limit_reached` (HTTP 409) |

A test bot (handle ending in `TESTBOT`) can join only test groups created by its owner or one of its testers, and only its owner or a tester can add it. Any other combination fails with `test_bot_access_denied`. An ordinary bot can't join a test group.

## Communities <a href="#communities" id="communities"></a>

A community groups Supergroups, groups and channels under one page. A Supergroup inside a community is an ordinary Supergroup for your bot: it is added, receives messages and is moderated in the same way. The Bot API has no community-level operations. Treat each Supergroup as a separate chat ID.

## In this section <a href="#in-this-section" id="in-this-section"></a>

- [Add your bot to a group](groups/adding-bots.md): how people add it, links that open the add flow, suggested rights and leaving.
- [What your bot receives](groups/privacy.md): privacy mode, routing rules, history and commands.
- [Administrator rights](groups/admin-rights.md): reading members, promoting, permissions and group information.
- [Moderation](groups/moderation.md): bans, restrictions, channel senders, deleting and pinning.
- [Invite links and join requests](groups/invites-and-requests.md): links, approvals, guard bots and member updates.
- [Forum topics](groups/topics.md): posting in topics and managing them.
