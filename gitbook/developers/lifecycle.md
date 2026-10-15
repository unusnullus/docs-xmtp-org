---
description: "Transfer ownership, restart delivery after a handover and delete a bot."
---

# Ownership and bot lifecycle

You can transfer a bot to another ARX account or delete it through NexoCreator. These actions require your ARX account and approval from a verified device. A verified device is a Messenger installation you have verified for your ARX account. You use it to approve sensitive actions such as a transfer or deletion. Your bot token cannot approve them.

## Approve an owner action <a href="#approve-an-owner-action" id="approve-an-owner-action"></a>

Open the relevant NexoCreator workflow, review the selected bot and enter the confirmation phrase. Approve the action on your verified device as well. You can complete the phrase and device approval in either order.

The confirmation lasts five minutes and applies only to the action you reviewed. If it expires or the bot changes, restart the workflow. An incorrect phrase does not consume the confirmation. Use the workflow's refresh action if you are unsure whether the request completed. Repeating an accepted action recovers its result instead of applying it again.

## Offer and accept a transfer <a href="#offer-and-accept-a-transfer" id="offer-and-accept-a-transfer"></a>

1. Ask the recipient to start a chat with the bot you're transferring and keep that bot unblocked.
2. Send `/transfer` to `@NEXOCREATOR`, select the bot and identify the recipient. Review the offer and approve it.
3. Ask the recipient to open `/transfers`, review the offer, accept the current developer terms and complete their own confirmation and device approval.
4. Check that the transfer is accepted before moving the running service.

The recipient must be an eligible human account in good standing with room in its [bot ownership allowance](limits.md#bot-profile-and-ownership). An offer lasts 72 hours. You keep ownership and your program keeps running until the recipient accepts. You can cancel an open offer, and the recipient can reject it. Expiration, suspension or deletion closes an outstanding offer without transferring ownership.

Official bots cannot be transferred or deleted through these owner workflows.

## What changes at acceptance <a href="#what-changes-at-acceptance" id="what-changes-at-acceptance"></a>

| Preserved | Requires action from the new bot owner |
| --- | --- |
| Bot identity, handle and profile | Issue a new bot token. |
| Commands and settings | Review settings before resuming the service. |
| Library `file_id` values | Check that the service still has access to the files it needs. |
| Existing chats | Select polling or a webhook and resume delivery. |

Acceptance immediately revokes the old credentials and pauses delivery. The webhook signing grace used during ordinary token rotation does not apply. The old program sees HTTP 401 `unauthorized`.

The new bot owner cannot receive queued updates from before the transfer. Users may see a delivery failure and choose to resend. Do not automatically repeat old business actions after the handover. A Bot API update is not a substitute for checking the acceptance result in NexoCreator.

## Move the program to the new bot owner <a href="#move-the-program-to-the-new-bot-owner" id="move-the-program-to-the-new-bot-owner"></a>

Stop the old workers and remove their credentials. The new bot owner should issue a token with `/token`, store it securely and configure the chosen update transport. Start one poller if using polling, or verify the new webhook before resuming delivery.

Transfer your database and hosting access separately, after reviewing which user data the recipient may receive. Bot ownership does not move your deployment, database or secret manager. Reconcile work already accepted by your service before sending anything with a new idempotency key, and update your privacy policy and support contact.

## Delete a bot <a href="#delete-a-bot" id="delete-a-bot"></a>

Send `/deletebot`, select your bot and review the effects. Enter the phrase and approve the action on your verified device. Deletion starts asynchronously. Use the refresh action until NexoCreator reports that the bot is deleted; do not treat the initial acceptance as completion.

Deletion revokes credentials, stops bot operations and is irreversible. Stop background jobs when authentication fails or the API returns HTTP 410 `bot_deleted`. Users retain their own chat history until they clear it.

The handle stops resolving and enters a quarantine of 30 days by default. During quarantine, you can recreate the handle while your account still exists. This creates a new bot identity and does not restore credentials or stored bot state.

Review the data retained by your own service when you shut down. Clearing an ARX chat does not erase copies held in your database or by your providers. Continue with [Security and privacy](security.md) and the [user-facing privacy guide](privacy.md).
