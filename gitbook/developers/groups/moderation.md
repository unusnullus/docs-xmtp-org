---
description: "Ban, remove and restrict members, block channel senders, delete messages and pin announcements as a group administrator."
---

# Moderate a group

An administrator bot can enforce a group's rules: remove or ban members, mute them for a while, block channels that post spam, delete messages and pin announcements. Each operation needs a specific [administrator right](admin-rights.md).

| Task | Operation | Right |
| --- | --- | --- |
| Ban a member | [`members.ban`](../reference/groups.md#operation-members-ban) | `can_restrict_members` |
| Lift a ban, or remove a member | [`members.unban`](../reference/groups.md#operation-members-unban) | `can_restrict_members` |
| Restrict or unrestrict a member | [`members.restrict`](../reference/groups.md#operation-members-restrict) | `can_restrict_members` |
| Block or unblock a channel sender | [`sender_chats.ban`](../reference/groups.md#operation-sender-chats-ban), [`sender_chats.unban`](../reference/groups.md#operation-sender-chats-unban) | `can_restrict_members` |
| Delete other members' messages | [`messages.delete`](../reference/messages.md#operation-messages-delete), [`messages.delete_batch`](../reference/messages.md#operation-messages-delete-batch) | `can_delete_messages` |
| Pin and unpin | [`messages.pin`](../reference/messages.md#operation-messages-pin), [`messages.unpin`](../reference/messages.md#operation-messages-unpin), [`messages.unpin_all`](../reference/messages.md#operation-messages-unpin-all) | `can_pin_messages` |

Every moderation operation follows the same hierarchy. Your bot can't act on the group owner, on itself, on other bots, or on an administrator outside its promotion chain. These attempts fail with `not_enough_rights` (HTTP 403), or `capability_not_available` (HTTP 403) when the target is a bot. A missing right fails with `not_enough_rights` and names it in `details.required_right`.

Send an `Idempotency-Key` with every moderation request, and reuse the same key and body when you retry after a lost response. See [Making requests](../making-requests.md#idempotency).

## Ban a member <a href="#ban-a-member" id="ban-a-member"></a>

`members.ban` removes a person from the group and prevents them from rejoining until the ban is lifted or expires. You can also ban someone who is not a member, by user ID, to keep them out.

| Field | Rule |
| --- | --- |
| `until_at` | Optional RFC 3339 time when the ban ends. Omit it for a permanent ban. A ban shorter than 30 seconds or longer than 366 days from now is permanent. |
| `revoke_messages` | Optional. In Supergroups it is always effective as `true`: the person loses access to the group and its history. Their messages stay visible to others. |

The group shows a `member_left` service message with `reason` set to `banned`. Administrator bots that opted in to `chat_member` receive the membership change.

## Remove a member or lift a ban <a href="#remove-a-member-or-lift-a-ban" id="remove-a-member-or-lift-a-ban"></a>

`members.unban` has two uses:

| Body | Effect |
| --- | --- |
| `{}` | Removes the person if they are a member and leaves them free to rejoin. Use it to remove someone without a ban. A banned person is unbanned. |
| `{"only_if_banned":true}` | Lifts an existing ban and does nothing else. |

`members.unban` never adds anyone back. The person rejoins through an invite link or a join request.

## Restrict a member <a href="#restrict-a-member" id="restrict-a-member"></a>

`members.restrict` replaces what one person may do, within the group's [default permissions](admin-rights.md#set-default-permissions). A restriction can only narrow the defaults: a permission the group denies stays denied.

| Field | Rule |
| --- | --- |
| `permissions` | Required. The permissions the person keeps. Omitted permissions follow the same rules as default permissions; `{}` removes every permission, which mutes the person. |
| `until_at` | Optional end time. Omitted, shorter than 30 seconds or longer than 366 days means permanent. |
| `use_independent_chat_permissions` | Optional. `true` saves the values exactly, without the implication rules. |

To lift a restriction, send every permission as `true`. A restriction can be set before the person joins.

## Mute a member and remove a message <a href="#mute-a-member-and-remove-a-message" id="mute-a-member-and-remove-a-message"></a>

This example mutes a member for one hour, deletes the message that broke the rules and tells the group. Set `CHAT_ID`, `USER_ID`, `MESSAGE_ID` and `REQUEST_KEY`. Your bot needs `can_restrict_members` and `can_delete_messages`.

Before you call this from a command such as `/mute`, check that the person who sent the command is an administrator with `can_restrict_members`. Read their status with [`members.get`](admin-rights.md#read-members); a command list doesn't restrict who can type a command.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
const userId = process.env.USER_ID;
const messageId = process.env.MESSAGE_ID;
if (!token || !key || !chatId || !userId || !messageId) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID, USER_ID, MESSAGE_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const until = new Date(Date.now() + 60 * 60 * 1000).toISOString();
  await bot.membersRestrict(chatId, userId, {
    key: `${key}-mute`,
    body: { permissions: {}, until_at: until },
  });
  await bot.messagesDelete(chatId, messageId, { key: `${key}-delete`, body: {} });
  await bot.messagesSend(chatId, {
    key: `${key}-notice`,
    body: {
      content: { type: "text", text: "A message was removed. Its sender is muted for one hour." },
      silent: true,
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
from datetime import datetime, timedelta, timezone
from arx_nexo import Client, Config, FormattedText, models

chat_id = os.environ["CHAT_ID"]
key = os.environ["REQUEST_KEY"]
with Client(
    Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")
) as bot:
    until = (datetime.now(timezone.utc) + timedelta(hours=1)).isoformat()
    bot.members_restrict(
        chat_id,
        os.environ["USER_ID"],
        body=models.GroupRestrictMember(permissions=models.GroupPermissions(), until_at=until),
        key=f"{key}-mute",
    )
    bot.messages_delete(chat_id, os.environ["MESSAGE_ID"], key=f"{key}-delete")
    bot.messages_send(
        chat_id,
        body=models.SendMessage(
            content=FormattedText(
                "A message was removed. Its sender is muted for one hour."
            ).content(),
            silent=True,
        ),
        key=f"{key}-notice",
    )
```

{% endtab %}
{% tab title="Rust" %}

Add `chrono` to your dependencies for the end time.

```rust
use arx_nexo::models::{GroupPermissions, GroupRestrictMember, SendMessage};
use arx_nexo::{Client, Config, FormattedText};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let user_id = std::env::var("USER_ID")?;
    let message_id = std::env::var("MESSAGE_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let until = (chrono::Utc::now() + chrono::Duration::hours(1)).to_rfc3339();
    let mute = GroupRestrictMember {
        until_at: Some(until),
        ..GroupRestrictMember::new(GroupPermissions::default())
    };
    bot.members_restrict(&chat_id, &user_id, &[], Some(&mute), Some(&format!("{key}-mute")))
        .await?;
    bot.messages_delete(&chat_id, &message_id, &[], None, Some(&format!("{key}-delete")))
        .await?;
    let notice = SendMessage {
        silent: Some(true),
        ..SendMessage::new(
            FormattedText::new("A message was removed. Its sender is muted for one hour.", vec![])?
                .content()?,
        )
    };
    bot.messages_send(&chat_id, &[], Some(&notice), Some(&format!("{key}-notice")))
        .await?;
    Ok(())
}
```

{% endtab %}
{% endtabs %}

## Block a channel sender <a href="#block-a-channel-sender" id="block-a-channel-sender"></a>

People can post in a group as one of their channels. Such a message carries `sender_chat` with `kind` set to `channel`. `sender_chats.ban` with that channel's ID stops it from posting in the group. While the ban applies, the channel's owner also can't post through their other channels. `sender_chats.unban` lifts the ban on that exact channel; other bans on the same owner remain. Both operations take the body `{}`.

## Delete messages <a href="#delete-messages" id="delete-messages"></a>

`messages.delete` deletes one message for everyone. `messages.delete_batch` deletes up to 100 messages of the same group with `{"message_ids":["101","102"]}`.

| Rule | Error |
| --- | --- |
| Your bot can delete its own messages. Deleting other members' messages needs `can_delete_messages`. | `not_enough_rights` (HTTP 403) |
| A bot can delete a group message only within 48 hours after it was sent, including its own. | `message_too_old` (HTTP 403) |
| The group's creation message and topic creation messages can't be deleted. | `message_cant_be_deleted` (HTTP 403) |
| The message must exist and be visible to your bot. | `message_not_found` (HTTP 404) |

A batch skips IDs that are missing or can't be deleted and returns them in `skipped`; `deleted` lists the removed IDs. A batch that contains other members' messages needs `can_delete_messages` for the whole request. Subscribe to the opt-in `messages_deleted` update to learn about deletions by others. Delete a whole forum topic with [`topics.delete`](topics.md#manage-topics).

## Pin messages <a href="#pin-messages" id="pin-messages"></a>

`messages.pin` pins a message in the group; set `"silent":true` to pin without notifying members. `messages.unpin` removes one pin: send `{"message_id":"123"}`, or `{}` to unpin the pinned message with the highest message ID. `messages.unpin_all` removes every pin.

A group holds up to 1,000 pinned messages; a new pin beyond that fails with `pin_limit_reached` (HTTP 409). Each pin adds a `message_pinned` service message, which every bot in the group receives. `chats.get` returns the latest pin visible to your bot in `pinned_message`.

## React to messages <a href="#react-to-messages" id="react-to-messages"></a>

Your bot can set a reaction on a group message with [`reactions.set`](../reference/interactive.md#operation-reactions-set). The group's `available_reactions` and `max_reaction_count` from `chats.get` apply. A reaction outside them fails with `reaction_not_allowed` (HTTP 400).
