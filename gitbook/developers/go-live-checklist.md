---
description: "Prepare your bot for real users and recoverable failures."
---

# Go-live checklist

Check these items before inviting users to your bot. Use a [TESTBOT](testing.md#testbot) for Messenger checks, then repeat the essential flows with your regular bot's configuration.

## Bot and credentials <a href="#bot-and-credentials" id="bot-and-credentials"></a>

- Create the regular bot separately from your TESTBOT. Configure its profile, commands and privacy policy through [NexoCreator](nexocreator.md).
- Store the token in a secret manager or a server environment variable. Keep it out of repositories, browser code, URLs and logs. Follow [Authentication & tokens](authentication.md).
- Set the API base URL to `https://signal.arx.pro/bot-api/v1` and confirm the bot's identity with `me.get`.
- Practice a token rotation and a revocation. Confirm that every worker and your webhook verifier pick up the new token. See [Security](security.md#rotate-a-token).

## Update delivery <a href="#update-delivery" id="update-delivery"></a>

- Run exactly one poller, or configure an HTTPS webhook. Do not run both. Follow [Polling and webhooks](updates.md).
- For a webhook, verify the signature over the raw body, check the timestamp and set a `secret_token`. Run `webhook.test` and read `webhook.get` after deploying.
- Select only the update types you handle with `allowed_updates`, and opt in to `messages_deleted`, `message_reaction` or `chat_member` if you need them. See [Filters](updates.md#filters).
- Save accepted work before acknowledging delivery. Test repeated updates, a handler failure, a restart and graceful shutdown without losing or repeating important effects.
- Handle `updates_dropped` by logging it and reconciling the affected state.
- Answer callback queries promptly, and answer `pre_checkout_query` and guard `chat_join_request` updates before their `expires_at`.

## Requests and errors <a href="#requests-and-errors" id="requests-and-errors"></a>

- Persist idempotency keys for actions you may resume after a restart. Check [Making requests](making-requests.md#idempotency) before retrying an uncertain outcome.
- Branch on `error.code`, not on the message text. Stop sending to a chat on `bot_blocked_by_user`, `chat_not_started` or `user_deactivated`. See [Errors](errors.md).
- Load effective sending limits from `platform.get`, honor `retry_after` and pace each chat separately. Confirm that a slow or blocked chat does not stop unrelated work. See [Limits](limits.md).
- Log `request_id` with failures, without tokens or message content.

## Content, groups and payments <a href="#content-groups-and-payments" id="content-groups-and-payments"></a>

- Test the content types you actually use, including file readiness, invalid input and permission failures. Follow [Testing](testing.md).
- If your bot works in groups, set its group privacy with `/setprivacy` and group joining with `/setjoingroups` in NexoCreator, and decide which administrator rights it needs. Check the sender's identity and role before acting on a command or button press in a group.
- If your bot accepts payments, deliver goods only after the `successful_payment` service message, keep the `pay_seq` order for each payer and test refunds. Follow [Payments](payments.md).

## Privacy and operations <a href="#privacy-and-operations" id="privacy-and-operations"></a>

- Publish a privacy policy that explains what you store, for how long, which providers receive data and how users contact you. Read [Privacy](privacy.md) with your user-facing text in mind.
- Verify that blocking stops new interaction and that your deletion handling covers your own stored copies.
- Watch NexoCreator [Alerts](alerts.md), use `/delivery` to inspect delivery, and prepare a support report without secrets using [Support](support.md).

Once these checks pass, keep following the [changelog](changes.md) as your bot serves users.
