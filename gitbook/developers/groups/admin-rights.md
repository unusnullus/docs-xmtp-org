---
description: "Check your bot's rights in a group, read members, promote people, set default permissions and change the group's information."
---

# Administrator rights

A group administrator decides which rights your bot gets. Each group operation checks your bot's current rights at the time of the request. Read them from the group instead of assuming what was granted when your bot joined.

## Rights and what they allow <a href="#rights-and-what-they-allow" id="rights-and-what-they-allow"></a>

| Right | Your bot can |
| --- | --- |
| `can_manage_chat` | Act as an administrator. ARX sets it whenever any other right is granted. An administrator bot receives every group message. |
| `can_delete_messages` | Delete other members' messages and delete forum topics. |
| `can_restrict_members` | Ban, unban, remove and restrict members, ban channel senders and change the group's default permissions. |
| `can_promote_members` | Promote members, change administrators it promoted and set their custom titles. |
| `can_change_info` | Set and delete the group photo. The Bot API can't change the title or description yet. |
| `can_invite_users` | Create and manage its own invite links, approve or decline join requests and act as a join request guard. |
| `can_pin_messages` | Pin and unpin messages. |
| `can_manage_topics` | Create, edit, close and reopen forum topics, and post in closed topics. |
| `can_manage_tags` | Change member tags. The Bot API has no operation for member tags yet. |
| `can_send_welcome_messages` | Stored and returned with the other rights. No Bot API operation uses it yet. |

`can_post_messages` and `can_edit_messages` apply to channels. Story, video chat and direct message rights are not available in groups. A bot administrator can't be anonymous and can't be the group owner.

Rights alone are not enough for actions on other administrators. Your bot can change, restrict or ban an administrator only when that administrator was promoted by your bot, directly or through administrators your bot promoted. It can never act on the owner or on itself, and it can't moderate or promote other bots.

## Read members <a href="#read-members" id="read-members"></a>

| Operation | Returns | Requirement |
| --- | --- | --- |
| [`members.get`](../reference/groups.md#operation-members-get) | One member's status and rights or restrictions. `user_id` is a decimal user ID or an exact `@username`. | Membership to read your bot itself. Administrator status to read anyone else. |
| [`members.list_admins`](../reference/groups.md#operation-members-list-admins) | The owner and administrators. Other bots are excluded unless you set `include_bots=true`; your bot is included when it is an administrator. | Membership |
| [`members.count`](../reference/groups.md#operation-members-count) | The number of members, as an integer. | Membership |
| [`chats.get`](../reference/platform.md#operation-chats-get) | Title, description, forum mode, default `permissions`, slow mode, join settings, your bot's primary `invite_link` and the pinned message visible to it. `has_visible_history` and `has_aggressive_anti_spam_enabled` are returned to administrators only. | Membership |

`members.get` returns a status-specific object selected by `status`:

| `status` | Fields beside `user` |
| --- | --- |
| `owner` | `custom_title`, `is_anonymous` |
| `administrator` | Every `can_*` right, `can_be_edited`, `custom_title`, `is_anonymous` |
| `member` | `tag`, `until_at` |
| `restricted` | `is_member`, `tag`, each permission it keeps, `until_at` |
| `left` | None |
| `banned` | `until_at` |

## Promote a member <a href="#promote-a-member" id="promote-a-member"></a>

`members.promote` replaces the administrator rights of a human member. Send every right the member should have; omitted rights are `false`, and `{"rights":{}}` demotes the member.

| Rule | Error |
| --- | --- |
| Your bot needs `can_promote_members`. | `not_enough_rights` (HTTP 403), with `details.required_right` |
| Your bot can grant only rights it holds. | `not_enough_rights` |
| The target must be a current human member, not the owner, and not an administrator outside your bot's promotion chain. | `chat_not_found` (HTTP 404) or `not_enough_rights` |
| The target can't be another bot. | `capability_not_available` (HTTP 403) |
| A group has at most 50 administrators. | `invalid_rights` (HTTP 400), with reason `administrator_limit` |

`members.set_custom_title` sets the title shown next to an administrator your bot promoted directly. The title contains up to 16 characters, without emoji or control characters. An empty title clears it.

This example checks that your bot can promote, makes a member a moderator with two of its own rights and gives them a title. Set `CHAT_ID`, `USER_ID` and `REQUEST_KEY`.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
const userId = process.env.USER_ID;
if (!token || !key || !chatId || !userId) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID, USER_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const me = await bot.meGet();
  const self = await bot.membersGet(chatId, me.id);
  if (
    self.status !== "administrator" ||
    !self.can_promote_members ||
    !self.can_delete_messages ||
    !self.can_pin_messages
  ) {
    throw new Error("The bot needs can_promote_members, can_delete_messages and can_pin_messages");
  }
  await bot.membersPromote(chatId, userId, {
    key: `${key}-promote`,
    body: { rights: { can_delete_messages: true, can_pin_messages: true } },
  });
  await bot.membersSetCustomTitle(chatId, userId, {
    key: `${key}-title`,
    body: { custom_title: "Moderator" },
  });
  console.log(await bot.membersGet(chatId, userId));
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
user_id = os.environ["USER_ID"]
key = os.environ["REQUEST_KEY"]
with Client(
    Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")
) as bot:
    me = bot.me_get()
    member = bot.members_get(chat_id, me.id)
    if not (
        member.status == "administrator"
        and member.can_promote_members
        and member.can_delete_messages
        and member.can_pin_messages
    ):
        raise SystemExit(
            "The bot needs can_promote_members, can_delete_messages and can_pin_messages"
        )
    bot.members_promote(
        chat_id,
        user_id,
        body=models.GroupPromoteMember(
            rights=models.GroupAdminRights(can_delete_messages=True, can_pin_messages=True)
        ),
        key=f"{key}-promote",
    )
    bot.members_set_custom_title(
        chat_id,
        user_id,
        body=models.GroupSetCustomTitle(custom_title="Moderator"),
        key=f"{key}-title",
    )
    print(bot.members_get(chat_id, user_id))
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::models::{GroupAdminRights, GroupMember, GroupPromoteMember, GroupSetCustomTitle};
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let user_id = std::env::var("USER_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let me = bot.me_get(&[], None, None).await?;
    let ready = match bot.members_get(&chat_id, &me.id, &[], None, None).await? {
        GroupMember::GroupMemberAdministrator(admin) => {
            admin.can_promote_members
                && admin.can_delete_messages
                && admin.can_pin_messages.unwrap_or(false)
        }
        _ => false,
    };
    if !ready {
        return Err("The bot needs can_promote_members, can_delete_messages and can_pin_messages".into());
    }
    let promote = GroupPromoteMember::new(GroupAdminRights {
        can_delete_messages: Some(true),
        can_pin_messages: Some(true),
        ..Default::default()
    });
    bot.members_promote(&chat_id, &user_id, &[], Some(&promote), Some(&format!("{key}-promote")))
        .await?;
    let title = GroupSetCustomTitle::new("Moderator".into());
    bot.members_set_custom_title(&chat_id, &user_id, &[], Some(&title), Some(&format!("{key}-title")))
        .await?;
    println!("{:?}", bot.members_get(&chat_id, &user_id, &[], None, None).await?);
    Ok(())
}
```

{% endtab %}
{% endtabs %}

## Set default permissions <a href="#set-default-permissions" id="set-default-permissions"></a>

`chats.set_permissions` replaces what ordinary members may do. It needs `can_restrict_members`. The body is `{"permissions":{...}}`. An omitted permission is `false`, with three exceptions: an omitted `can_react_to_messages` takes the value of `can_send_messages`, and omitted `can_edit_tag` and `can_manage_topics` take the value of `can_pin_messages`. Unknown permission names fail with `invalid_request`.

| Permission | Allows members to |
| --- | --- |
| `can_send_messages` | Send text, contacts, locations and venues. |
| `can_send_photos`, `can_send_videos`, `can_send_audios`, `can_send_documents`, `can_send_voice_notes`, `can_send_video_notes` | Send that media type. |
| `can_send_polls` | Send polls and checklists. |
| `can_send_other_messages` | Send animations, stickers, dice and content through inline bots. |
| `can_add_web_page_previews` | Show link previews. |
| `can_react_to_messages` | React to messages. |
| `can_invite_users` | Add members and bots. |
| `can_pin_messages` | Pin messages. Not granted to members of a public group. |
| `can_change_info` | Change group information. Not granted to members of a public group. |
| `can_manage_topics` | Create topics in a forum. |
| `can_edit_tag` | Set their own member tag. |

ARX normalizes the permissions before saving them. `can_send_other_messages` or `can_add_web_page_previews` turns on `can_send_messages` and every media permission; `can_send_polls` turns on `can_send_messages`. Set `use_independent_chat_permissions` to `true` to save the values exactly as sent. An unchanged result has no effect and sends no update.

A member restriction can only narrow these defaults. See [Restrict a member](moderation.md#restrict-a-member).

## Change group information <a href="#change-group-information" id="change-group-information"></a>

Use Messenger's group settings to change the title or description. The photo operations below require `can_change_info`. Members see a service message for each change, and every bot in the group receives it.

| Operation | Body | Rules |
| --- | --- | --- |
| [`chats.set_photo`](../reference/groups.md#operation-chats-set-photo) | `{"photo":{"file_id":"..."}}` | A JPEG, PNG or WebP photo of up to 10 MiB, given by `file_id` or URL. Setting the current photo again has no effect. |
| [`chats.delete_photo`](../reference/groups.md#operation-chats-delete-photo) | `{}` | Removing an absent photo succeeds without effect. |

See [Files](../files.md) for uploading a photo and reusing its `file_id`.
