---
description: "Find every Nexo bot capability and the guide that covers it."
---

# Capabilities

Find what your bot needs to do, then follow the linked guide. Each guide lists the operations, fields, limits and errors for that capability, with TypeScript, Python and Rust examples.

Some capabilities need a setting that you turn on in [NexoCreator](nexocreator.md). Call [`me.get`](reference/platform.md#operation-me-get) to see your bot's configuration. `settings` reports the options you control, such as `can_join_groups` and `can_read_all_group_messages`. The `features` object reports `available` for Mini Apps and payments when they are available to your bot, and for groups (`nexo_groups`) and each advanced mode when you have turned them on. Its `stickers`, `channels`, `inline`, `games` and `stories` entries always report `disabled`; do not use them to decide what your bot can do. Before you sell, call `getPaymentReadiness`, as [Set up payments](payments/setup.md#check-readiness) explains.

## Conversations and updates <a href="#conversations-and-updates" id="conversations-and-updates"></a>

| You want to | Read |
| --- | --- |
| Reply to a message | [Quickstart](quickstart.md) |
| Choose between polling and a webhook | [Receiving updates](receiving-updates.md) |
| Configure polling, signed webhooks and update filters | [Polling and webhooks](updates.md) |
| Handle retries, idempotency keys and errors | [Making requests](making-requests.md) and [Errors](errors.md) |
| Open your bot with a start parameter, or link a user account | [Deep links](deep-links.md) |
| Look up a chat | [`chats.get`](reference/platform.md#operation-chats-get) |

## Messages and content <a href="#messages-and-content" id="messages-and-content"></a>

| You want to | Read |
| --- | --- |
| Format text with entities, ARX Markdown or rich content | [Formatting](formatting.md) |
| Upload, send or download photos, videos, audio, voice notes and documents | [Files](files.md) |
| Send text, media and other content | [Send messages](messages.md) |
| Send an album | [Albums](messages/albums.md) |
| Send stickers and dice | [Stickers and dice](messages/stickers-and-dice.md) |
| Reply to a message and quote part of it | [Replies and quotes](messages/replies.md) |
| Edit or delete messages | [Edit and delete](messages/edit-and-delete.md) |
| Copy or forward messages | [Copy and forward](copy-and-forward.md) |
| Stream a draft or show a typing action | [Drafts](drafts.md) |
| Add a message effect | [Message effects](message-effects.md) |
| Run polls and quizzes, or track tasks with checklists | [Polls and checklists](polls-and-checklists.md) |
| Share locations, live locations, venues or contacts | [Locations and contacts](locations-and-contacts.md) |
| React to a message, receive reaction updates, and pin or unpin messages | [Reactions and pins](messages/reactions-and-pins.md) |

## Buttons, commands and profile <a href="#buttons-commands-and-profile" id="buttons-commands-and-profile"></a>

| You want to | Read |
| --- | --- |
| Add inline buttons and answer callbacks | [Keyboards](keyboards.md) |
| Show a reply keyboard that requests a contact, location, poll or users | [Keyboards](keyboards.md#reply-keyboards) |
| Register commands, set a menu button and localize your profile | [Commands & profile](commands-and-profile.md) |
| Set profile photos and welcome media | [Files](files.md#profile-media) |

## Groups, topics and channels <a href="#groups-topics-and-channels" id="groups-topics-and-channels"></a>

| You want to | Read |
| --- | --- |
| Let people add your bot to groups, and choose its group privacy | [Bots in groups](groups.md) |
| Receive group messages, commands, mentions and member updates | [Bots in groups](groups.md) |
| Work in forum topics: create, edit, close, reopen and delete topics | [Forum topics](groups/topics.md) |
| Run a bot in groups that belong to a community | [Bots in groups](groups.md) |
| Moderate members: ban, restrict, promote and set custom titles | [Moderation](groups/moderation.md) and [Administrator rights](groups/admin-rights.md) |
| Manage invite links and approve or decline join requests | [Invite links and join requests](groups/invites-and-requests.md) |
| Request administrator rights when people add your bot | [Bots in groups](groups.md) and [`default_admin_rights.set`](reference/profile.md#operation-default-admin-rights-set) |
| Post text in a channel where your bot is an administrator | [Send messages](messages.md) and [Not available yet](not-available-yet.md#groups-and-channels) |
| Use topics in private chats with your bot, when available | [`topics.create`](reference/topics.md#operation-topics-create) |

## Mini Apps <a href="#mini-apps" id="mini-apps"></a>

| You want to | Read |
| --- | --- |
| Open a web app from a button, the menu button or your bot's profile | [Mini Apps](mini-apps.md) |
| Set up a Main Mini App, named Mini Apps and allowed origins | [Mini Apps](mini-apps.md) and [NexoCreator](nexocreator.md#set-up-mini-apps) |
| Send a result on behalf of the user who opened your Mini App | [Send results to your bot](mini-apps/send-data.md) |
| Prepare a message or button that your Mini App offers to the user | [Send results to your bot](mini-apps/send-data.md#share-a-prepared-message) |
| Review group join requests in a Mini App | [Guard join requests](groups/invites-and-requests.md#guard-join-requests) |

## Payments <a href="#payments" id="payments"></a>

| You want to | Read |
| --- | --- |
| Check whether your bot is ready to accept payments | [Set up payments](payments/setup.md#check-readiness) |
| Send a crypto invoice or create an invoice link | [Invoices and links](payments/invoices.md) |
| Approve a checkout and fulfil the purchase | [Check and confirm payments](payments/checkout.md) |
| Sell a subscription that the buyer renews each period | [Subscriptions and renewals](payments/subscriptions.md) |
| Check a user's paid access | [Subscriptions and renewals](payments/subscriptions.md#check-access) |
| Refund a charge | [Refunds](payments/refunds.md) |
| Read plans, accepted assets, balances and sales | [Balances and reports](payments/reports.md) and [Set up payments](payments/setup.md#read-your-plans) |

## Advanced modes <a href="#advanced-modes" id="advanced-modes"></a>

These modes act beyond your bot's own chats. You turn each one on under **Advanced modes** in NexoCreator after accepting its terms.

| You want to | Read |
| --- | --- |
| Help a person in the chats they choose, on their behalf (Business connections) | [Business connections](managed-bots/business-connections.md) |
| Create bots for people and manage their tokens and access (Manage bots) | [Managed bots](managed-bots/managed-bots.md) |
| Answer one request in a chat where your bot is not a member (Guest queries) | [Guest queries](managed-bots/guest-queries.md) |
| Exchange messages with other bots (Bot conversations) | [Advanced modes](managed-bots.md) |

## Build, test and operate <a href="#build-test-and-operate" id="build-test-and-operate"></a>

| You want to | Read |
| --- | --- |
| Install an SDK or the `arx-nexo` CLI | [SDKs and CLI](sdks.md) |
| Test against the local emulator or a TESTBOT | [Testing](testing.md) |
| Connect an AI agent through the MCP server | [MCP server](mcp.md) |
| Run complete example programs | [Examples](examples.md) |
| Manage tokens, delivery, ownership and health alerts | [NexoCreator](nexocreator.md) and [Alerts](alerts.md) |
| Size requests and stay within rate limits | [Limits](limits.md) |
| Prepare a bot for its users | [Go-live checklist](go-live-checklist.md) |

Check [Not available yet](not-available-yet.md) before you design around a feature that is not listed here.
