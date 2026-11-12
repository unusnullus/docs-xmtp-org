---
description: "Create and manage your bot's invite links, approve or decline join requests, act as a join request guard and follow member changes."
---

# Invite links and join requests

An administrator bot with `can_invite_users` can bring people into a group. It creates invite links, decides on requests to join, and can screen requests as the group's join request guard. With the `chat_member` update it also follows who joins and leaves.

## Invite links <a href="#invite-links" id="invite-links"></a>

Your bot manages only the links it created: one primary link per group and additional links, up to 1,000 active links in total. [`chats.get`](../reference/platform.md#operation-chats-get) returns your bot's primary link as `invite_link` when it has one.

| Operation | What it does |
| --- | --- |
| [`invite_links.create`](../reference/groups.md#operation-invite-links-create) | Creates an additional link and returns it. |
| [`invite_links.edit`](../reference/groups.md#operation-invite-links-edit) | Changes one of your bot's additional links. Omitted fields are kept; an empty `name` clears the name. `null` values are rejected. |
| [`invite_links.revoke`](../reference/groups.md#operation-invite-links-revoke) | Revokes one of your bot's links. Revoking the primary link replaces it with a new one. |
| [`invite_links.export`](../reference/groups.md#operation-invite-links-export) | Replaces your bot's primary link and returns the new URL as a string. |

Link paths use `invite_code`, the 22-character code of the link, not the full URL. A link object contains `invite_link`, `invite_code`, `creator`, `is_primary`, `is_revoked`, `creates_join_request`, and, when set, `name`, `expires_at`, `member_limit` and `pending_join_request_count`.

| Field | Rule |
| --- | --- |
| `creates_join_request` | Optional, default `false`. When `true`, people who open the link send a join request instead of joining. It can't be combined with `member_limit`. |
| `name` | Optional label of up to 32 characters, shown to administrators. |
| `expires_at` | Optional RFC 3339 time in the future. Omit it for a link that doesn't expire. |
| `member_limit` | Optional, 1 to 99,999. The most people who can be members through this link at the same time. |

| Code | HTTP | Cause |
| --- | --- | --- |
| `invite_link_not_editable` | 403 | The link belongs to another member, or you tried to edit a primary link. |
| `invite_link_invalid` | 404 | The code doesn't identify an active link in this group. |
| `invite_link_limit_reached` | 409 | Your bot already has 1,000 active links in this group. Revoke unused links. |
| `not_enough_rights` | 403 | Your bot lacks `can_invite_users`. |

## Join requests <a href="#join-requests" id="join-requests"></a>

A person sends a join request when they open a link with `creates_join_request` or when the group requires approval to join. An administrator bot with `can_invite_users` receives a `chat_join_request` update. It is in the default update filter.

| Field | Content |
| --- | --- |
| `chat` | The group. |
| `user` | The person asking to join. |
| `user_chat_id` | The private chat ID between this person and your bot. You can message them there only if they started your bot. |
| `bio` | The person's profile bio, when available. |
| `invite_link` | The link they used, when it is one your bot can see. |
| `at` | When the request was made. |
| `query_id` | Present only when your bot is the group's guard. See [Guard join requests](#guard-join-requests). |

Approve or decline with [`join_requests.approve`](../reference/groups.md#operation-join-requests-approve) or [`join_requests.decline`](../reference/groups.md#operation-join-requests-decline), passing the group's chat ID and the person's user ID with the body `{}`. A request stays pending for up to 30 days. A replayed approval or decline never acts on a later request by the same person.

| Code | HTTP | Cause |
| --- | --- | --- |
| `join_request_not_found` | 404 | No pending request from this person. |
| `join_request_already_processed` | 409 | Someone else already approved or declined it, or it expired. |

## Guard join requests <a href="#guard-join-requests" id="guard-join-requests"></a>

A guard bot screens join requests before the group's administrators see them, for example to ask the person to complete a check. To act as a guard, your bot needs:

1. Join request guard turned on with `/setjoinrequests` in [NexoCreator](../nexocreator.md). `me.get` then returns `settings.supports_join_request_queries` as `true`.
2. Administrator status in the group, with `can_invite_users`.
3. An administrator selecting your bot under **Join request guard** in the group's settings.

The guard's `chat_join_request` update carries `query_id` and `expires_at`. You have 10 seconds from the request to act, or less if the request expires sooner. Within that time, either answer the query or open your Mini App for the applicant:

| Operation | Body | Effect |
| --- | --- | --- |
| [`join_request_queries.answer`](../reference/groups.md#operation-join-request-queries-answer) | `{"result":"approve"}`, `{"result":"decline"}` or `{"result":"queue"}` | Approves, declines, or passes the request to the administrators. |
| [`join_request_queries.open_mini_app`](../reference/groups.md#operation-join-request-queries-open-mini-app) | `{"url":"https://..."}` | Shows the applicant your bot's registered HTTPS Mini App. The deadline becomes 15 minutes after the first open, and retries don't extend it. Answer the query when the check ends. |

The URL must use HTTPS, be at most 2,048 characters and have an origin you registered with `/setapporigins`; otherwise the call fails with `mini_app_origin_not_registered` (HTTP 400). Opening the Mini App never approves anyone. An answer after the deadline fails with `query_expired` (HTTP 410); a second answer with another key fails with `query_already_answered` (HTTP 409).

## Approve requests from a list <a href="#approve-requests-from-a-list" id="approve-requests-from-a-list"></a>

This example creates an approval link, prints it, and handles join requests. People whose user IDs are in `ALLOWED_USER_IDS` (comma-separated) are approved. A guard bot passes everyone else to the administrators; a notice is left pending. Set `CHAT_ID`, `ALLOWED_USER_IDS` and `REQUEST_KEY`.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
const allowed = new Set((process.env.ALLOWED_USER_IDS ?? "").split(",").filter(Boolean));
if (!token || !key || !chatId) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const link = await bot.inviteLinksCreate(chatId, {
    key: `${key}-link`,
    body: { creates_join_request: true, name: "Website" },
  });
  console.log(`Share ${link.invite_link}`);
  let offset: string | undefined;
  while (true) {
    const batch = await bot.updatesGet({
      query: { timeout_seconds: 30, ...(offset ? { offset } : {}) },
    });
    for (const update of batch.updates) {
      const request = update.type === "chat_join_request" ? update.chat_join_request : undefined;
      if (request) {
        const approve = allowed.has(request.user.id);
        if (request.query_id) {
          await bot.joinRequestQueriesAnswer(request.query_id, {
            key: `guard-${update.update_id}`,
            body: { result: approve ? "approve" : "queue" },
          });
        } else if (approve) {
          await bot.joinRequestsApprove(request.chat.id, request.user.id, {
            key: `approve-${update.update_id}`,
            body: {},
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
from arx_nexo import Client, Config, models

chat_id = os.environ["CHAT_ID"]
key = os.environ["REQUEST_KEY"]
allowed = {item for item in os.environ.get("ALLOWED_USER_IDS", "").split(",") if item}
with Client(
    Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")
) as bot:
    link = bot.invite_links_create(
        chat_id,
        body=models.GroupCreateInviteLink(creates_join_request=True, name="Website"),
        key=f"{key}-link",
    )
    print(f"Share {link.invite_link}")
    offset: str | None = None
    while True:
        batch = bot.updates_get(offset=offset, timeout_seconds=30)
        for update in batch.updates:
            request = update.chat_join_request
            if request is not None:
                approve = request.user.id in allowed
                if request.query_id is not None:
                    bot.join_request_queries_answer(
                        request.query_id,
                        body=models.AnswerJoinRequestQuery(
                            result="approve" if approve else "queue"
                        ),
                        key=f"guard-{update.update_id}",
                    )
                elif approve:
                    bot.join_requests_approve(
                        request.chat.id, request.user.id, key=f"approve-{update.update_id}"
                    )
            offset = str(int(update.update_id) + 1)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use std::collections::HashSet;

use arx_nexo::models::{
    AnswerJoinRequestQuery, AnswerJoinRequestQueryResult, GroupCreateInviteLink, Update,
};
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let list = std::env::var("ALLOWED_USER_IDS").unwrap_or_default();
    let allowed: HashSet<&str> = list.split(',').filter(|id| !id.is_empty()).collect();
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let request = GroupCreateInviteLink {
        creates_join_request: Some(true),
        name: Some("Website".into()),
        ..Default::default()
    };
    let link = bot
        .invite_links_create(&chat_id, &[], Some(&request), Some(&format!("{key}-link")))
        .await?;
    println!("Share {}", link.invite_link);
    let mut offset: Option<String> = None;
    loop {
        let mut query = vec![("timeout_seconds", "30")];
        if let Some(value) = offset.as_deref() {
            query.push(("offset", value));
        }
        let batch = bot.updates_get(&query, None, None).await?;
        for update in batch.updates {
            if let Update::GroupUpdateChatJoinRequest(item) = &update {
                let join = &item.chat_join_request;
                let approve = allowed.contains(join.user.id.as_str());
                if let Some(query_id) = &join.query_id {
                    let result = if approve {
                        AnswerJoinRequestQueryResult::Approve
                    } else {
                        AnswerJoinRequestQueryResult::Queue
                    };
                    let answer = AnswerJoinRequestQuery::new(result);
                    let key = format!("guard-{}", update.update_id());
                    bot.join_request_queries_answer(query_id, &[], Some(&answer), Some(&key))
                        .await?;
                } else if approve {
                    let key = format!("approve-{}", update.update_id());
                    bot.join_requests_approve(&join.chat.id, &join.user.id, &[], None, Some(&key))
                        .await?;
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

## Member updates <a href="#member-updates" id="member-updates"></a>

Every bot in a group receives service messages when members join or leave: `members_joined` lists the new members, and `member_left` carries `reason` set to `banned` or `kicked` when an administrator or bot removed the member. The message sender is the moderator in that case; a voluntary departure has no `reason`.

For full membership changes, an administrator bot can opt in to `chat_member` updates. Add `chat_member` to `allowed_updates`, for example `default,chat_member`. See [Filters](../updates.md#filters). Each update covers one member:

| Field | Content |
| --- | --- |
| `chat` | The group. |
| `user` | The account that made the change: the member, an administrator or a bot. |
| `old`, `new` | The member's status before and after, with rights or restrictions. A person who was not a member appears as `left`. |
| `invite_link` | The invite link used to join, when your bot can see it. |
| `via_join_request` | `true` when the member joined through an approved join request. |
| `at` | When the change happened. |

Your bot receives `chat_member` updates only while it is an administrator. Changes to your bot itself arrive as `my_chat_member` instead. See [Detect when your bot joins or leaves](adding-bots.md#detect-when-your-bot-joins-or-leaves).
