---
description: "Post in forum topics, follow topic events and create, edit, close, reopen or delete topics in a Supergroup with topics."
---

# Forum topics

A forum is a Supergroup whose conversation is split into topics. `chat.is_forum` is `true` in updates and in `chats.get`. Every forum has a General topic with ID `1`; other topics are created by members, administrators and bots.

## Topic IDs <a href="#topic-ids" id="topic-ids"></a>

A topic's ID is the `message_id` of the `topic_created` service message that opened it. It never changes and is not reused after the topic is deleted. Messages in a topic carry `topic_id`. Keep it with the chat ID when you store a conversation.

Your bot learns about topics from service messages, which every bot in the group receives:

| `service.type` | Fields |
| --- | --- |
| `topic_created` | `name`, `icon_color`, `is_name_implicit`, and `icon_key` when set |
| `topic_edited` | The changed `name` or `icon_key`. An empty `icon_key` means the icon was removed. |
| `topic_closed`, `topic_reopened` | None |
| `general_topic_hidden`, `general_topic_unhidden` | None |

The Bot API doesn't list or read topics in a group forum: `topics.list`, `topics.get` and `topics.unpin_all` return `capability_not_available` (HTTP 403) for group chats. Track topics from these service messages, and read a topic's messages with [`messages.history`](privacy.md#read-group-history) and its `topic_id` query field.

## Post in a topic <a href="#post-in-a-topic" id="post-in-a-topic"></a>

Set `topic_id` in `messages.send` and the other send methods. Omit it to post in General. When you reply to a message, send the `topic_id` of that message too.

| Code | HTTP | Cause |
| --- | --- | --- |
| `forum_disabled` | 409 | The group doesn't use topics. Omit `topic_id`. |
| `topic_not_found` | 404 | The topic doesn't exist or was deleted. |
| `topic_closed` | 403 | The topic is closed. Your bot can still post with `can_manage_topics`. |

Privacy mode works the same in every topic. See [What your bot receives](privacy.md).

## Manage topics <a href="#manage-topics" id="manage-topics"></a>

| Operation | Requirement | Notes |
| --- | --- | --- |
| [`topics.create`](../reference/topics.md#operation-topics-create) | `can_manage_topics` | Returns the new topic. Members see a `topic_created` service message. |
| [`topics.edit`](../reference/topics.md#operation-topics-edit) | `can_manage_topics`, or your bot created the topic | Renames it or changes its icon. General requires `can_manage_topics`. |
| [`topics.close`](../reference/topics.md#operation-topics-close) | `can_manage_topics`, or your bot created the topic | Only administrators with `can_manage_topics` can post in a closed topic. General requires `can_manage_topics`. |
| [`topics.reopen`](../reference/topics.md#operation-topics-reopen) | `can_manage_topics`, or your bot created the topic | Makes the topic visible and open again. General requires `can_manage_topics`. |
| [`topics.delete`](../reference/topics.md#operation-topics-delete) | `can_delete_messages` and an `Idempotency-Key` | Deletes the topic and all its messages. General can't be deleted. |
| [`topics.list_icons`](../reference/topics.md#operation-topics-list-icons) | None | Lists icon keys, colors and SVG vectors. Cache the result by its `version`. |

| Field | Rule |
| --- | --- |
| `name` | 1 to 128 characters, not only spaces. In `topics.edit`, omit it or send `""` to keep the current name. |
| `icon_color` | Optional on create. One of `7322096`, `16766590`, `13338331`, `9367192`, `16749490` and `16478047`. It can't be changed later through the Bot API. |
| `icon_key` | Optional. One of `chat`, `book`, `idea`, `code`, `heart` and `star`. In `topics.edit`, `""` removes the icon. |
| `expected_version` | Optional on edit, close, reopen and delete. The change fails with `version_conflict` (HTTP 409) if the topic's current `version` differs. |

`topics.create`, `topics.edit`, `topics.close` and `topics.reopen` return the topic: `topic_id`, `name`, `icon_color`, `icon_key`, `is_closed`, `is_hidden`, `is_name_implicit`, `version`, `last_message_id` and `capabilities`. `capabilities` tells you which of `edit`, `close`, `reopen`, `delete`, `hide`, `unhide` and `unpin_all` your bot can use on that topic now. An unchanged edit returns the same `version` and adds no service message.

A group holds up to 10,000 active topics. Creating more fails with `topics_limit_reached` (HTTP 409).

### Delete a topic <a href="#delete-a-topic" id="delete-a-topic"></a>

`topics.delete` removes the topic and every message in it. The first response confirms acceptance with `lifecycle_state` set to `deleting` and `purge_complete` set to `false`. Repeat the request with the same key and body to follow progress until `lifecycle_state` is `deleted` and `purge_complete` is `true`. The topic ID stays reserved.

## Create a topic and post in it <a href="#create-a-topic-and-post-in-it" id="create-a-topic-and-post-in-it"></a>

This example creates a topic, posts a welcome message in it and closes it so that only administrators with `can_manage_topics` can post. Set `CHAT_ID` to a forum where your bot has `can_manage_topics`, and `REQUEST_KEY`.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const key = process.env.REQUEST_KEY;
const chatId = process.env.CHAT_ID;
if (!token || !key || !chatId) {
  throw new Error("Set ARX_BOT_TOKEN, REQUEST_KEY, CHAT_ID");
}
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const topic = await bot.topicsCreate(chatId, {
    key: `${key}-create`,
    body: { name: "Announcements", icon_key: "star" },
  });
  if (!("topic_id" in topic) || !topic.topic_id) {
    throw new Error("Unexpected topic result");
  }
  await bot.messagesSend(chatId, {
    key: `${key}-welcome`,
    body: {
      content: { type: "text", text: "Release notes are posted here." },
      topic_id: topic.topic_id,
    },
  });
  await bot.topicsClose(chatId, topic.topic_id, { key: `${key}-close`, body: {} });
  console.log(`Topic ${topic.topic_id} is ready`);
} finally {
  await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, FormattedText, models

chat_id = os.environ["CHAT_ID"]
key = os.environ["REQUEST_KEY"]
with Client(
    Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")
) as bot:
    topic = bot.topics_create(
        chat_id,
        body=models.TopicsCreate(
            models.GroupForumCreate(name="Announcements", icon_key="star")
        ),
        key=f"{key}-create",
    )
    if topic.topic_id is None:
        raise SystemExit("Unexpected topic result")
    bot.messages_send(
        chat_id,
        body=models.SendMessage(
            content=FormattedText("Release notes are posted here.").content(),
            topic_id=topic.topic_id,
        ),
        key=f"{key}-welcome",
    )
    bot.topics_close(
        chat_id, topic.topic_id, body=models.GroupForumClose(), key=f"{key}-close"
    )
    print(f"Topic {topic.topic_id} is ready")
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::models::{
    GroupForumClose, GroupForumCreate, GroupForumCreateIconKey, SendMessage, TopicMutationResult,
    TopicsCreate,
};
use arx_nexo::{Client, Config, FormattedText};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let key = std::env::var("REQUEST_KEY")?;
    let chat_id = std::env::var("CHAT_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let create = TopicsCreate::GroupForumCreate(Box::new(GroupForumCreate {
        icon_key: Some(GroupForumCreateIconKey::Star),
        ..GroupForumCreate::new("Announcements".into())
    }));
    let topic_id = match bot
        .topics_create(&chat_id, &[], Some(&create), Some(&format!("{key}-create")))
        .await?
    {
        TopicMutationResult::ForumTopic(topic) => topic.topic_id,
        TopicMutationResult::PrivateTopic(_) => return Err("Unexpected topic result".into()),
    };
    let welcome = SendMessage {
        topic_id: Some(topic_id.clone()),
        ..SendMessage::new(FormattedText::new("Release notes are posted here.", vec![])?.content()?)
    };
    bot.messages_send(&chat_id, &[], Some(&welcome), Some(&format!("{key}-welcome")))
        .await?;
    bot.topics_close(
        &chat_id,
        &topic_id,
        &[],
        Some(&GroupForumClose::default()),
        Some(&format!("{key}-close")),
    )
    .await?;
    println!("Topic {topic_id} is ready");
    Ok(())
}
```

{% endtab %}
{% endtabs %}
