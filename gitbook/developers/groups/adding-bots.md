---
description: "Learn how people add your bot to a Supergroup, share a link that opens the add flow, suggest administrator rights and leave a group."
---

# Add your bot to a group

People add your bot to a Supergroup from Messenger. Your bot can't join a group on its own. You can make the flow shorter with an add link and with the administrator rights your bot suggests.

## How people add your bot <a href="#how-people-add-your-bot" id="how-people-add-your-bot"></a>

1. The person opens your bot's profile and chooses **Add to group**.
2. Messenger lists the Supergroups where the person can add bots. Forums are labeled **Forum**.
3. The person can turn on **Add as administrator** and choose the rights to grant. Your [suggested rights](#suggest-administrator-rights) are preselected.
4. The person confirms. The group shows that the bot joined.

Who can add your bot depends on the person's role in the group:

| Added as | The person needs |
| --- | --- |
| Member | The right to add members: an administrator with `can_invite_users`, or any member when the group lets members invite people. |
| Administrator | `can_promote_members`. The person can grant only rights they hold themselves. |

Your bot must allow groups (`/setjoingroups` in [NexoCreator](../nexocreator.md)), be active, and not be banned from that group. A group holds up to 20 bots and up to 50 administrators.

## Detect when your bot joins or leaves <a href="#detect-when-your-bot-joins-or-leaves" id="detect-when-your-bot-joins-or-leaves"></a>

Every change to your bot's own membership arrives as a `my_chat_member` update. In a group, the payload contains `chat`, `user` (the account that made the change), `old`, `new` and `at`. `old.status` and `new.status` are `owner`, `administrator`, `member`, `restricted`, `left` or `banned`.

| Change | `old.status` | `new.status` |
| --- | --- | --- |
| Added as a member | `left` | `member` |
| Added as an administrator, or promoted | `left` or `member` | `administrator` |
| Rights changed | `administrator` | `administrator` |
| Demoted | `administrator` | `member` |
| Removed | any | `left` |
| Banned | any | `banned` |

An administrator `new` value lists your bot's rights, such as `can_delete_messages` and `can_restrict_members`. Save the chat ID and these rights when your bot joins. Read them again with [`members.get`](admin-rights.md#read-members) before an action that depends on a right.

`my_chat_member` is in the default update filter. An update you already received stays valid after your bot leaves, but every later operation in that group needs current membership.

## Share an add link <a href="#share-an-add-link" id="share-an-add-link"></a>

An add link opens the add flow for your bot in Messenger:

```text
https://www.arx.pro/bot/ACMESUPPORTBOT?startgroup=welcome&admin=delete_messages+pin_messages
```

| Parameter | Rule |
| --- | --- |
| `startgroup` | Required; it can be empty. Its value is an optional payload of 1 to 64 characters from `A-Z`, `a-z`, `0-9`, `_` and `-`. ARX Messenger drops an invalid payload and still opens the add flow. |
| `admin` | Optional. Rights to preselect, separated by `+`. Its presence also preselects **Add as administrator**. |

Don't combine `startgroup` with `start`, `startapp`, `startattach`, `profile`, `text` or other bot-link parameters, and use each parameter once. ARX Messenger doesn't open such a link.

The `admin` values for groups are `manage_chat`, `delete_messages`, `restrict_members`, `promote_members`, `change_info`, `invite_users`, `pin_messages`, `manage_topics`, `manage_tags` and `send_welcome_messages`. Messenger ignores unknown values. The rights are suggestions: the person can change them, and the group grants only what the person is allowed to grant.

When a payload is present and the bot is added, Messenger posts `/start@ACMESUPPORTBOT welcome` in the group as a message from the person who added it. Your bot receives it as a command addressed to it, with the person as `sender_user`. Use the payload to link the group to an account or setup flow in your service. Treat it as untrusted input.

## Suggest administrator rights <a href="#suggest-administrator-rights" id="suggest-administrator-rights"></a>

`default_admin_rights.set` stores the rights your bot suggests when someone adds it as an administrator. Set the query field `target` to `groups`. These defaults grant nothing; each group decides what to grant.

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
  await bot.defaultAdminRightsSet({
    query: { target: "groups" },
    key,
    body: {
      rights: {
        can_delete_messages: true,
        can_restrict_members: true,
        can_pin_messages: true,
      },
    },
  });
  console.log(await bot.defaultAdminRightsGet({ query: { target: "groups" } }));
} finally {
  await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models

with Client(
    Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")
) as bot:
    rights = models.AdminRightsValue(
        can_delete_messages=True,
        can_restrict_members=True,
        can_pin_messages=True,
    )
    bot.default_admin_rights_set(
        query={"target": "groups"},
        body=models.SetAdminRights(rights=models.AdminRights(rights)),
        key=os.environ["REQUEST_KEY"],
    )
    print(bot.default_admin_rights_get(query={"target": "groups"}))
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::models::{AdminRightsValue, SetAdminRights};
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [("target", "groups")];
    let request = SetAdminRights::new(Some(AdminRightsValue {
        can_delete_messages: Some(true),
        can_restrict_members: Some(true),
        can_pin_messages: Some(true),
        ..Default::default()
    }));
    bot.default_admin_rights_set(&query, Some(&request), Some(&key)).await?;
    let current = bot.default_admin_rights_get(&query, None, None).await?;
    println!("{current:?}");
    Ok(())
}
```

{% endtab %}
{% endtabs %}

| Rule | Detail |
| --- | --- |
| Omitted rights | Mean `false`. |
| `can_manage_chat` | Set to `true` automatically when you suggest any right other than `is_anonymous`. |
| `can_post_messages`, `can_edit_messages` | Channel rights. They must be `false` for `groups`; `true` fails with `invalid_parameter` (HTTP 400). |
| `can_manage_video_chats`, `can_post_stories`, `can_edit_stories`, `can_delete_stories`, `can_manage_direct_messages` | Not available. `true` fails with `capability_not_available` (HTTP 403). |
| `is_anonymous` | Accepted as a suggestion, but a bot administrator can't be anonymous in a group. |
| Reset | Send `{"rights":null}` to clear the suggestion. |

You can also set the suggestion with `/setdefaultadminrights` in NexoCreator. The typed methods are `defaultAdminRightsSet` and `defaultAdminRightsGet` in TypeScript and `default_admin_rights_set` and `default_admin_rights_get` in Python and Rust. See [`default_admin_rights.set`](../reference/profile.md#operation-default-admin-rights-set).

## Leave a group <a href="#leave-a-group" id="leave-a-group"></a>

Call `chats.leave` with the group's chat ID and the body `{}`. Your bot leaves using its own membership, receives a `my_chat_member` update with `new.status` set to `left`, and loses access to the group. Reuse the same key and body if the response is lost; a replay never removes a newer membership.

The typed method is `chatsLeave` in TypeScript and `chats_leave` in Python and Rust. See [`chats.leave`](../reference/groups.md#operation-chats-leave).

## Errors when your bot is added <a href="#errors-when-your-bot-is-added" id="errors-when-your-bot-is-added"></a>

People see these errors in Messenger. They explain why your bot never received a `my_chat_member` update.

| Code | HTTP | Cause |
| --- | --- | --- |
| `bot_cannot_join_groups` | 403 | The bot doesn't allow groups. Turn on `/setjoingroups`. |
| `group_bots_limit_reached` | 409 | The group already has 20 bots. |
| `group_admins_limit_reached` | 409 | The group already has 50 administrators. |
| `user_banned_from_chat` | 403 | The bot is banned from this group. An administrator must lift the ban. |
| `test_bot_access_denied` | 403 | A test bot and a group don't match. |
| `capability_not_available` | 403 | The bot is inactive or restricted by ARX moderation. |
| `access_restricted` | 403 | The bot is a managed bot with restricted access, and the person isn't on its access list. See [Managed bots](../managed-bots/managed-bots.md). |
| `rights_not_subset` | 403 | The person tried to grant rights they don't hold. |
| `not_enough_rights` | 403 | The person can't add members or promote administrators. |
