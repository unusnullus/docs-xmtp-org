---
description: "Create your bot and manage its profile, credentials and delivery in ARX."
---

# NexoCreator

You can create and manage your bots by chatting with `@NEXOCREATOR` in ARX. Use your ARX account, select a bot from `/mybots`, or add `@HANDLE` to a command that asks you to select one.

## Create your first bot <a href="#create-your-first-bot" id="create-your-first-bot"></a>

1. Send `/newbot`, or `/newtestbot` for a bot available only to you and invited testers.
2. Read and accept the developer terms shown in NexoCreator if prompted.
3. Enter the bot's name and handle. Check the [handle and account requirements](limits.md#bot-profile-and-ownership) before choosing them.
4. Keep the creation progress message until NexoCreator confirms the result. Open `/mybots` to configure the bot and retrieve its credential.

A test bot uses a handle ending in `TESTBOT`. Bot handles share ARX's handle namespace. You cannot rename your bot's handle. Repeating an accepted action does not create another bot. `/cancel` closes a dialog but cannot undo work already accepted.

NexoCreator shows the applicable developer terms and records acceptance for your account. If a new version requires acceptance, review the displayed text and deadline. Your bot's privacy policy is a separate document that you choose for its users.

## Find your way around <a href="#find-your-way-around" id="find-your-way-around"></a>

| Command | What you can do |
| --- | --- |
| `/start` | Open creation, your bot list, documentation and news. |
| `/help` | Read the command menu and open available support and official bot links. |
| `/mybots` | Choose a bot and open its profile, token, commands, settings, delivery or alerts. |
| `/cancel` | Close the current dialog and invalidate its buttons. |
| `/empty` | Clear optional text or media when the current prompt allows it. |

You can continue a dialog on another device with the same account. NexoCreator updates the existing dialog message. A dialog expires after 60 minutes without your activity. If a button is stale, refresh the dialog before trying again. If settings changed elsewhere, reload them before saving.

## Edit your profile and command menu <a href="#edit-your-profile-and-command-menu" id="edit-your-profile-and-command-menu"></a>

| Command | What to provide |
| --- | --- |
| `/setname` | A bot, language and display name. |
| `/setdescription` | A bot, language and description. |
| `/setabouttext` | A bot, language and short description. |
| `/setprivacypolicy` | An HTTPS policy URL, the standard ARX bot policy, or the option to clear it. |
| `/setcommands` | A bot, scope and language, followed by `command - description` lines. |
| `/deletecommands` | A bot, scope and language, then confirmation that you want to remove that list. |
| `/setmenubutton` | Choose Commands to show the command menu, or Default to reset it. |

The command editor shows the current list and identifies invalid lines. A suggestion to add `/start` or `/help` does not block saving. Implement each command in your program and check user permissions yourself. See [Commands and profile](commands-and-profile.md) for fields, language selection and SDK examples, and [Limits](limits.md#bot-profile-and-ownership) for sizes.

## Profile media <a href="#profile-media" id="profile-media"></a>

Use `/setuserpic` for an avatar or `/setdescriptionpic` for media shown in an empty bot chat. Select the bot, then send the original media to NexoCreator. Use `/empty` to remove it when prompted.

NexoCreator copies the media independently, so deleting your source message does not remove the profile image. See [Profile media](files.md#profile-media) for formats, sizes, video requirements and the Bot API equivalents.

## Credentials <a href="#credentials" id="credentials"></a>

Use `/token` to issue or rotate a credential, and `/revoke` to revoke it without a replacement. Open the credential reveal only when you are ready to save it securely. The token is not sent as ordinary chat text.

Follow [Rotate a token](security.md#rotate-a-token) for the complete sequence, delivery choices and webhook signing transition. Use [Authentication and tokens](authentication.md) to configure your program.

## Testers and alerts <a href="#testers-and-alerts" id="testers-and-alerts"></a>

Use `/testers` to add or remove people who can interact with a test bot. Removing a tester also removes their access to its protected profile media. The tester allowance is listed in [Limits](limits.md#bot-profile-and-ownership).

Open `/alerts` to inspect notices, acknowledge a fix, mute notifications or reactivate them. [Alerts](alerts.md) explains the conditions and available actions.

## Delivery diagnostics and recovery <a href="#delivery-diagnostics-and-recovery" id="delivery-diagnostics-and-recovery"></a>

Send `/delivery` to inspect the update transport, filters, webhook host, connection limit and selected IP address. The view also shows queue size and age, discarded updates, sanitized errors and polling activity. It hides the webhook path and query because they may contain secrets.

| Action | What happens |
| --- | --- |
| Pause | Stops delivery and keeps queued updates. |
| Resume | Restarts delivery when your bot is available and has a current token. |
| Drop pending updates | Discards the queue after confirmation. |
| Delete webhook | Removes the webhook after confirmation, keeps queued updates and selects polling. |

A confirmation expires after five minutes or after the delivery configuration changes. Deleting a webhook does not discard updates. When every webhook attempt returns HTTP 410 for more than 23 hours, ARX removes the webhook, keeps updates for polling and notifies you.

Use [Updates](updates.md) to configure filters, acknowledge deliveries and choose SDK helpers.

## Transfer or delete your bot <a href="#transfer-or-delete-your-bot" id="transfer-or-delete-your-bot"></a>

Use `/transfer` to offer a bot to another account, `/transfers` to review sent and received offers, and `/deletebot` to delete a bot permanently. These workflows require a confirmation phrase and verified-device approval.

Read [Ownership and bot lifecycle](lifecycle.md) before confirming. It explains which credentials stop working, what the new bot owner must configure and what happens to existing chats and data.
