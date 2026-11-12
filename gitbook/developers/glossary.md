---
description: "Understand the terms used by the Nexo Bot API and guides."
---

# Glossary

Use these terms to connect the guides with the field names you receive from the API. Wire fields keep their original names even when the documentation uses a more readable label.

| Term | Meaning |
| --- | --- |
| ARX | The product brand. ARX Markdown is its supported Markdown format. |
| Nexo | The ARX platform and API for bots. |
| NexoCreator | The ARX bot you use to create and manage your bots. Its handle is `@NEXOCREATOR`. |
| Bot owner | The ARX account that owns a bot and manages it through NexoCreator. |
| Bot token | A secret that authenticates your program as one bot. |
| Handle | The public account or bot name. The API exposes this as `username`. |
| User ID | A string identifying an ARX account. |
| Chat ID | A string identifying a private chat, group or channel. Your bot replies to the chat ID from an update. |
| Message ID | A string identifying a message within its chat. Keep it together with the chat ID. |
| Update | An event delivered to your bot, such as a message or callback. |
| Update ID | The string in `update_id`. Use it to detect duplicate delivery. |
| Polling | Repeated requests that wait for and retrieve updates. |
| Offset | The position from which polling continues. You send it in the `offset` parameter. |
| Webhook | An HTTPS endpoint where your program receives signed deliveries. |
| Idempotency key | A value that lets you retry the same API action without repeating its effect while the replay record remains valid. |
| File ID | An opaque reference that lets your bot use a file it can access. |
| TESTBOT | A bot restricted to its bot owner and invited testers. |
| Regular bot | A bot you create with `/newbot` for your service's users, with its own credentials and files. |
| Reveal window | The time allowed to view a newly issued token once. It does not determine the token's lifetime. |
| Group | A multi-member chat that your bot can join. Its chat has `kind` set to `group`. |
| Forum topic | A separate thread inside a group with forum topics turned on (`is_forum`). |
| Community | A collection of groups and channels under one page. A bot works in each group it joins. |
| Supergroup | The ARX group type that bots can join, with administrators, forum topics and bots. Its chat has `kind` set to `group`. |
| Group privacy | A bot setting. When on, the bot receives only commands, mentions and replies to its messages in groups. |
| Join request guard | A bot setting that lets the bot review requests to join a group through its Main Mini App. |
| Mini App | A web app that opens inside ARX Messenger from your bot's buttons, menu button or profile. |
| Main Mini App | The Mini App linked to your bot's profile. A bot has at most one. |
| Invoice | A payment request your bot sends for a one-time purchase or a subscription, paid in a crypto asset. |
| Subscription | Access that the buyer renews each period by approving a new payment. Nothing renews automatically. |
| Advanced modes | Business connections, Manage bots, Guest queries and Bot conversations. You turn each one on in NexoCreator. |
| Business connection | A link that lets your bot help a person in the chats they choose, on their behalf. |
| Managed bot | A bot that your bot created for a person through Manage bots. The person owns it; your bot can manage its token and access. |
| Guest query | A single request from a chat your bot is not a member of, which your bot answers once. |
| Emulator | A local Bot API simulator that uses synthetic credentials for testing your program. |
| KiB / MiB | Binary size units: 1 KiB is 1,024 bytes and 1 MiB is 1,048,576 bytes. |

Continue with [Making requests](making-requests.md) for envelopes, IDs and retries, or [Receiving updates](receiving-updates.md) for event delivery.
