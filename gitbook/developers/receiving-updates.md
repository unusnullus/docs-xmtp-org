---
description: "Choose polling or a webhook to receive events for your bot."
---

# Receiving updates

Connect your program to the events your bot receives: messages in private chats and groups, button presses, membership changes, join requests, payments and delivery notices. Each event is an update with an `update_id` that you use to detect repeats.

## Choose how updates reach you <a href="#choose-how-updates-reach-you" id="choose-how-updates-reach-you"></a>

| Approach | Use it when | Your program needs |
| --- | --- | --- |
| Polling | You want a simple worker or a local development loop. | Outbound HTTPS access and one active poller for the bot. |
| Webhook | You already run an HTTPS service and want incoming requests. | A public HTTPS endpoint and raw-body signature verification. |

Choose one approach for each bot. A configured webhook prevents polling: `updates.get` returns `webhook_active` until you call `webhook.delete`. Queued updates survive the switch in both directions unless you ask to drop them. The SDKs provide polling and webhook helpers in TypeScript, Python and Rust, so choose based on how you run your application.

## Select the updates you need <a href="#select-the-updates-you-need" id="select-the-updates-you-need"></a>

Your bot receives a default set of update types. Some types, such as `messages_deleted`, `message_reaction` and `chat_member`, arrive only after you opt in through `allowed_updates`. `updates_dropped` always arrives, so you learn when updates were lost. [Update types](updates.md#update-types) lists every type and [Filters](updates.md#filters) explains how to change the selection.

## Accept work before acknowledging it <a href="#accept-work-before-acknowledging-it" id="accept-work-before-acknowledging-it"></a>

Treat delivery as at least once. Process an update or save it durably before acknowledging it, and make repeated delivery safe. Use `update_id` for delivery deduplication and a stable action key for any reply or external effect that must happen once.

With polling, set the offset to the last processed update ID plus one to acknowledge every update up to that ID. With webhooks, a 2xx response acknowledges the delivery. If a process stops after acknowledgement but before saving its work, that work can be lost.

Some updates have a deadline. Answer a `callback_query` promptly, and answer a `pre_checkout_query` or a guard `chat_join_request` before its `expires_at`. Keep these handlers fast and independent of slower work in the same chat.

The [Polling and webhooks guide](updates.md) covers the full delivery rules, update types, filters, webhook settings, signatures and SDK helpers. Follow it to implement your chosen transport, then use [Testing](testing.md) to check duplicate delivery and restarts.
