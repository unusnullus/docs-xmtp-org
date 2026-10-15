---
description: "Protect your bot token, verify incoming updates and handle user data."
---

# Security and privacy

You can protect your bot's credentials and limit the data your service retains. Bot chats are not encrypted end to end: ARX processes their content and delivers it to your bot. Explain this to users before asking them to send sensitive information.

## Protect your bot token <a href="#protect-your-bot-token" id="protect-your-bot-token"></a>

Load the token from a secret manager or an environment variable on your server. Send it in the `Authorization: Bearer ...` header to the API base URL for your environment, over HTTPS. Disable redirects on authenticated requests. Use HTTP only for a local emulator, with loopback access explicitly enabled.

Keep tokens out of browser code, URLs, repositories, analytics and exception messages. Your bot does not need your ARX session. Signed file URLs also grant access, so keep them out of logs and send no bot token when using them.

The credential reveal is single use and expires after 60 minutes by default. This is the time allowed to retrieve the token, not the token's lifetime. Store the revealed credential securely. If you lose it, rotate it in NexoCreator.

## Rotate a token <a href="#rotate-a-token" id="rotate-a-token"></a>

1. Open `@NEXOCREATOR` with your ARX account (the bot owner's) and send `/token`. Select your bot, or use `/token @HANDLE`.
2. Choose rotation with delivery continuing or paused. Pausing is useful when you need time to update your webhook verifier or deployment.
3. Review and confirm the change. The old token stops authenticating new requests immediately, so expect a brief interruption while you replace it. Requests the API already accepted may still complete.
4. Open the credential reveal and save the replacement in your secret manager. Update every worker and your webhook verification key, then restart the affected processes.
5. If you paused delivery, open `/delivery` and resume it. Check `me.get`, then confirm that polling or your webhook receives a new test message.

After rotation, webhook deliveries can carry signatures from both the old and new keys for one hour. Accept a valid signature from either key during that interval. API requests have no corresponding grace period. Follow the [webhook verification guide](updates.md#verify-webhook-deliveries) for key derivation, raw-body verification and timestamp checks.

If a token leaks, revoke it immediately with NexoCreator's `/revoke` command or the Bot API's `token.revoke` operation. Revocation pauses delivery and removes webhook signing grace. Issue a replacement with `/token`, update your service and explicitly resume delivery when it is ready.

## Respond to suspension <a href="#respond-to-suspension" id="respond-to-suspension"></a>

While suspended, every call returns 403 `bot_suspended`, and your bot does not receive normal update delivery. Stop sending and pause your workers instead of retrying the same failure. Review your notice in NexoCreator and use [Support](support.md) if you need help.

Suspension also cancels outstanding ownership offers. It does not itself rotate the token. After the suspension is lifted, check the token and delivery state before restarting work. A separate restriction can allow existing chats to continue at lower rates; read the effective [sending policy](limits.md#effective-sending-policy).

## Verify updates before acting <a href="#verify-updates-before-acting" id="verify-updates-before-acting"></a>

For webhooks, verify the signature over the raw request bytes, validate the timestamp and check the secret header you set before parsing or accepting work. Compare signatures in constant time and keep your clock synchronized. IP allowlisting, when `platform.get.webhook_egress_cidrs` supplies ranges, is an additional check.

Save an update and its `update_id` durably before acknowledging it. Use your own receipt for external effects such as sending an email, so redelivery does not repeat the action. The API's idempotency key covers API operations, not effects in your own services. See [Updates](updates.md) for acknowledgement and redelivery rules.

## Validate user input and files <a href="#validate-user-input-and-files" id="validate-user-input-and-files"></a>

Authorize commands and callbacks on your server. A visible command or button does not establish permission. Keep sensitive callback state behind a short opaque identifier, and make account-linking codes expire after one use.

Limit file size and processing time, sanitize filenames and isolate converters. Treat extensions and supplied MIME types as untrusted, even when ARX has validated the media. Follow the [file access and retention rules](files.md#file-identity-and-retention) when deciding what to store.

If your service fetches user-supplied URLs, validate the destination address and every redirect. Restrict protocols, response size and deadlines. Never forward your bot token to the destination.

## Explain your data use <a href="#explain-your-data-use" id="explain-your-data-use"></a>

Your bot can receive user IDs, available handles, language and shared content. Contacts and locations require the user's action. A manually entered contact is unverified input. The Bot API does not supply other conversations, user IP addresses, device IDs or encryption keys.

Publish a privacy policy that explains purpose, retention, external providers and a contact for data requests. Routine logs should use a sanitized `request_id` rather than message content or credentials. Opt in to `messages_deleted` if your service needs deletion updates, and define how those updates affect your own stored copies.

Users can report abuse without sharing their report, evidence or identity with the bot owner. Blocking and deletion have separate effects, described in [Privacy](privacy.md).

For a change of bot owner or permanent shutdown, follow [Ownership and bot lifecycle](lifecycle.md).
