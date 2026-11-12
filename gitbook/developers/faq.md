---
description: "Find answers to common questions about Nexo bot integrations."
---

# FAQ

Resolve common integration questions, then follow the linked guide for the steps and edge cases.

## Can my bot read a user's other conversations? <a href="#can-my-bot-read-a-users-other-conversations" id="can-my-bot-read-a-users-other-conversations"></a>

No. Your bot receives content from its own chats and from the groups it belongs to. In a group with group privacy on, it receives only commands, mentions and replies to its messages. The exceptions are a Business connection, where a person chooses which of their chats your bot can help with, and a guest query, where a user sends your bot one message from another chat. Read [Privacy](privacy.md) before collecting or forwarding personal data.

## Can my bot send the first message? <a href="#can-my-bot-send-the-first-message" id="can-my-bot-send-the-first-message"></a>

Not until the user allows it. The user must start the chat, or allow messages from your Mini App with `requestWriteAccess` (your bot then receives a `write_access_allowed` service message). Reply using `message.chat.id` from an update, as shown in the [quickstart](quickstart.md). In a group your bot belongs to, it can post whenever the group's permissions allow it. See [Bots in groups](groups.md).

## Can I use polling and a webhook at the same time? <a href="#can-i-use-polling-and-a-webhook-at-the-same-time" id="can-i-use-polling-and-a-webhook-at-the-same-time"></a>

No. Choose one delivery method for a bot. See [Receiving updates](receiving-updates.md) to choose and [Updates](updates.md) to switch safely.

## Can the same update arrive more than once? <a href="#can-the-same-update-arrive-more-than-once" id="can-the-same-update-arrive-more-than-once"></a>

Yes. Save processed update IDs and make your handler safe to repeat. The [updates guide](updates.md) explains when to acknowledge delivery.

## Does the SDK retry every failed request? <a href="#does-the-sdk-retry-every-failed-request" id="does-the-sdk-retry-every-failed-request"></a>

No. It retries eligible failures within its attempt and time limits. Files streamed from paths or readers need an explicit retry with a fresh source. See [SDK retries](sdks.md#retries-and-idempotency).

## Can I reveal the same token again? <a href="#can-i-reveal-the-same-token-again" id="can-i-reveal-the-same-token-again"></a>

No. Save it when you first reveal it. If you lose it or miss the reveal window, rotate it through NexoCreator. See [Authentication & tokens](authentication.md).

## Can another bot reuse my file IDs? <a href="#can-another-bot-reuse-my-file-ids" id="can-another-bot-reuse-my-file-ids"></a>

No. File IDs depend on the receiving bot's access. The [files guide](files.md) explains reuse, readiness and downloads.

## Does a TESTBOT bypass limits or webhook requirements? <a href="#does-a-testbot-bypass-limits-or-webhook-requirements" id="does-a-testbot-bypass-limits-or-webhook-requirements"></a>

No. It provides private access for you and your testers while retaining the environment's effective limits and HTTPS webhook requirements. See [Testing](testing.md#testbot).

## Can a TESTBOT become a regular bot? <a href="#can-a-testbot-become-a-regular-bot" id="can-a-testbot-become-a-regular-bot"></a>

No. Create a regular bot separately, configure its credentials and upload its files. Use the [go-live checklist](go-live-checklist.md) to prepare the move.

## Can my bot work in groups? <a href="#can-my-bot-work-in-groups" id="can-my-bot-work-in-groups"></a>

Yes. Group access is on by default for new bots, and people can add your bot to their groups. Choose its group privacy and suggested admin rights in [NexoCreator](nexocreator.md#configure-groups), then follow [Bots in groups](groups.md).

## How do I know whether a feature is available to my bot? <a href="#how-do-i-know-whether-a-feature-is-available-to-my-bot" id="how-do-i-know-whether-a-feature-is-available-to-my-bot"></a>

Check the [capability map](features.md), then [Not available yet](not-available-yet.md). [`me.get`](reference/platform.md#operation-me-get) shows which settings and advanced modes are on for your bot.

## Does registering a command implement it? <a href="#does-registering-a-command-implement-it" id="does-registering-a-command-implement-it"></a>

No. Registration creates menu entries. You write and authorize the handlers in your program. See [Commands & profile](commands-and-profile.md).

If your question is about a failing request, start with [Errors](errors.md), then follow [Support](support.md) if you still need help.
