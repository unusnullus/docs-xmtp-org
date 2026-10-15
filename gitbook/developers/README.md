---
description: "Build and run a bot for private chats in ARX Messenger."
---

# Nexo bots

Build a bot that answers messages, processes files or runs an interactive service in ARX Messenger. You run your program and store its state. Nexo supplies the Bot API and delivers the events your program needs to respond.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Create your first bot</strong></td><td>Create a bot and reply in TypeScript, Python or Rust.</td><td><a href="quickstart.md">Create your first bot</a></td></tr><tr><td><strong>Choose an SDK</strong></td><td>Install a client and the local development tools.</td><td><a href="sdks.md">Choose an SDK</a></td></tr><tr><td><strong>Receive updates</strong></td><td>Choose polling or a webhook for your program.</td><td><a href="receiving-updates.md">Receive updates</a></td></tr><tr><td><strong>Explore the API</strong></td><td>Find operations, request fields and response types.</td><td><a href="api.md">Explore the API</a></td></tr></tbody></table>

## How your bot works <a href="#how-your-bot-works" id="how-your-bot-works"></a>

Create a bot through `@NEXOCREATOR` and save its token. When a user starts a conversation with your bot, Nexo delivers an update. Your program reads the chat ID from that update and uses the Bot API to reply.

You can [poll for updates](updates.md#polling) from a process without a public address, or receive them at an HTTPS webhook. Both approaches need duplicate handling and durable storage for work you cannot afford to lose.

## What you can build <a href="#what-you-can-build" id="what-you-can-build"></a>

Your bot can send text and media, collect answers with buttons and polls, share locations and contacts, and show a draft while it prepares a reply. The [capability map](features.md) links to the guide for each feature. The [examples](examples.md) provide complete programs in all three languages.

## Privacy in bot chats <a href="#privacy-in-bot-chats" id="privacy-in-bot-chats"></a>

Bot chats are not encrypted end to end. ARX processes their content, and you can read and store what users send to your bot. Explain your data use in your bot's privacy policy, including any external services you use. Your bot cannot read the user's other conversations. Read [Privacy](privacy.md) before collecting personal information.

Start with the [quickstart](quickstart.md), or use the [go-live checklist](go-live-checklist.md) if you already have a working bot.
