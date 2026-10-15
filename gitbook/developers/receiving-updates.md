---
description: "Choose polling or a webhook to receive events for your bot."
---

# Receiving updates

Connect your program to the events your bot receives, such as messages, commands and button presses. Each event is an update with an `update_id` that you can use to detect repeats.

## Choose how updates reach you <a href="#choose-how-updates-reach-you" id="choose-how-updates-reach-you"></a>

| Approach | Use it when | Your program needs |
| --- | --- | --- |
| Polling | You want a simple worker or a local development loop. | Outbound HTTPS access and one active poller for the bot. |
| Webhook | You already run an HTTPS service and want incoming requests. | A reachable HTTPS endpoint and raw-body signature verification. |

Choose one approach for each bot. A configured webhook prevents polling. The SDKs provide polling and webhook helpers in TypeScript, Python and Rust, so choose based on how you run your application.

## Accept work before acknowledging it <a href="#accept-work-before-acknowledging-it" id="accept-work-before-acknowledging-it"></a>

Treat delivery as at least once. Process an update or save it durably before acknowledging it, and make repeated delivery safe. Use `update_id` for delivery deduplication and a stable action key for any reply or external effect that must happen once.

With polling, set the offset to the last processed update ID plus one to acknowledge every update up to that ID. With webhooks, a successful HTTP response acknowledges the delivery. If a process stops after acknowledgement but before saving its work, that work can be lost.

The [Polling and webhooks guide](updates.md) covers the full delivery rules, supported update types, filters, webhook settings, signatures and SDK helpers. Follow it to implement your chosen transport, then use [Testing](testing.md) to check duplicate delivery and restarts.
