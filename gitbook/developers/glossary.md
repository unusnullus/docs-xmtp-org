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
| Chat ID | A string identifying a conversation. Your bot replies to the chat ID from an update. |
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
| Emulator | A local Bot API simulator that uses synthetic credentials for testing your program. |
| KiB / MiB | Binary size units: 1 KiB is 1,024 bytes and 1 MiB is 1,048,576 bytes. |

Continue with [Making requests](making-requests.md) for envelopes, IDs and retries, or [Receiving updates](receiving-updates.md) for event delivery.
