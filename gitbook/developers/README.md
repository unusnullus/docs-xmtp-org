---
description: "Build bots for private chats, groups, Mini Apps and payments in ARX Messenger."
---

# Nexo bots

Build a bot that answers messages, runs a group, opens a Mini App or sells access in ARX Messenger. You run your program and store its state. Nexo supplies the Bot API and delivers the events your program needs to respond.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Create your first bot</strong></td><td>Create a bot and reply in TypeScript, Python or Rust.</td><td><a href="quickstart.md">Create your first bot</a></td></tr><tr><td><strong>Choose an SDK</strong></td><td>Install a client and the local development tools.</td><td><a href="sdks.md">Choose an SDK</a></td></tr><tr><td><strong>Find a capability</strong></td><td>See everything a bot can do and the guide for each feature.</td><td><a href="features.md">Find a capability</a></td></tr><tr><td><strong>Explore the API</strong></td><td>Find operations, request fields and response types.</td><td><a href="api.md">Explore the API</a></td></tr></tbody></table>

## How your bot works <a href="#how-your-bot-works" id="how-your-bot-works"></a>

Create a bot in [NexoCreator](nexocreator.md) (`@NEXOCREATOR`) and save its token. When a user starts a chat with your bot, mentions it in a group or presses one of its buttons, Nexo delivers an update. Your program reads the chat ID from that update and uses the Bot API to reply.

You can [poll for updates](updates.md#polling) from a process without a public address, or receive them at an HTTPS webhook. Both approaches need duplicate handling and durable storage for work you cannot afford to lose.

## What you can build <a href="#what-you-can-build" id="what-you-can-build"></a>

- Conversations in private chats with text, media, [buttons](keyboards.md), [polls and checklists](polls-and-checklists.md), [locations and contacts](locations-and-contacts.md), and [drafts](drafts.md) that stream a reply.
- [Group bots](groups.md) that answer commands, work in forum topics, moderate members and review join requests.
- [Mini Apps](mini-apps.md) that open from a button or the menu.
- [Paid bots](payments.md) that send crypto invoices and sell subscriptions.
- [Advanced modes](managed-bots.md) in which your bot creates bots for people, helps them in their own chats or answers requests in chats it is not a member of.

The [capability map](features.md) links to the guide for each feature, and the [examples](examples.md) are complete programs in all three languages.

## Privacy in bot chats <a href="#privacy-in-bot-chats" id="privacy-in-bot-chats"></a>

Private chats with bots and the groups your bot joins are not end-to-end encrypted. ARX processes their content, and you can read and store what users send to your bot. In a group with group privacy on, your bot receives only commands, mentions and replies to its messages. Your bot cannot read a user's other conversations, except the chats a person chooses to share through a [Business connection](managed-bots.md). Explain your data use in your bot's privacy policy, including any external services you use, and read [Privacy](privacy.md) before you collect personal information.

Start with the [quickstart](quickstart.md), or use the [go-live checklist](go-live-checklist.md) if you already have a working bot.
