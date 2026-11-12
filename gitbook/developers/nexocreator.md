---
description: "Create bots and manage their profile, tokens, groups, Mini Apps, payments and advanced modes."
---

# NexoCreator

NexoCreator is the official ARX bot that creates your bots and issues their tokens. Open `@NEXOCREATOR` in ARX Messenger and send commands, or use its buttons. Every action applies to a bot you own.

Commands that act on one bot ask you to choose it first. To skip that step, add the bot's handle: `/setname @ACMESUPPORTBOT`. NexoCreator ignores a command addressed to another bot, such as `/start@OTHERBOT`.

Where the **Open NexoCreator** button appears in `/start` or `/help`, it opens NexoCreator as a Mini App. Payments and **Advanced modes** are set up there; the other sections are available in both the chat and the Mini App.

## Create a bot <a href="#create-a-bot" id="create-a-bot"></a>

1. Send `/newbot`. To create a bot that only you and your testers can use, send `/newtestbot`.
2. If NexoCreator shows the developer terms, read the summary and choose **I accept**. **Read the full terms** opens the complete text.
3. Send the display name, 1 to 64 characters.
4. Send the handle: 8 to 24 letters or digits, ending in `BOT`. A test bot's handle ends in `TESTBOT`. If the handle is taken, NexoCreator suggests up to three available ones as buttons.
5. Wait for **Your bot is ready.** The message shows the handle and the bot link `https://www.arx.pro/bot/<HANDLE>`.
6. Choose **View token** and save the token at once. See [Save the token](#save-the-token).

The confirmation also has **Configure**, which opens the bot's settings, and **Help**.

Your account must be at least 24 hours old to create a bot. By default you can own 20 bots and 10 test bots, create up to 10 bots a day, and wait 60 seconds between attempts. Handles share the ARX handle namespace, are stored in upper case and cannot be changed later. Display names that could impersonate ARX or NexoCreator are refused. See [Limits](limits.md#bot-profile-and-ownership).

Repeating an accepted step does not create a second bot. `/cancel` closes the current dialog, but it cannot undo work NexoCreator has already accepted.

## Find your way around <a href="#find-your-way-around" id="find-your-way-around"></a>

| Command | What you can do |
| --- | --- |
| `/start` | Open the main menu: **Create a bot**, **My bots**, **Ownership offers** and **Help**. |
| `/help` | Choose a topic to list its commands, or **Show all**. `/help all` lists every command. Help also links to the guide, updates and support. |
| `/mybots` | Choose a bot and open its settings. Each page lists 20 bots. |
| `/cancel` | Close the current dialog. |

A dialog expires after 60 minutes without activity; send a command to start again. You can continue a dialog on another device signed in to the same account. When a prompt allows it, send `/empty` to clear an optional value. NexoCreator accepts up to 60 inputs a minute.

`/mybots` opens a bot's menu with these sections:

| Section | What it contains |
| --- | --- |
| **Profile** | Name, about text, description, profile photo, welcome media and privacy policy. |
| **Commands and menu** | Command lists and the menu button. |
| **Mini Apps** | Main Mini App, named Mini Apps, attachment and side menus, the Settings item and allowed origins. Shown when Mini Apps are available. |
| **Groups and privacy** | Group access, group privacy, the join request guard, suggested admin rights and private chat topics. |
| **Advanced modes** | Business connections, Manage bots, Guest queries and Bot conversations. Opens the NexoCreator Mini App. |
| **Test access** | Testers of a test bot. |
| **Token and delivery** | Token status, rotation and revocation, and delivery diagnostics. |
| **Activity** | Moderation notices, health alerts and statistics. |
| **Ownership and data** | Configuration export and ownership transfer. |
| **Danger zone** | Permanent deletion. |

## Save the token <a href="#save-the-token" id="save-the-token"></a>

Choose **View token** to open the secure token view in Messenger. You can reveal a token once, within 60 minutes of issuing it by default. This is the reveal window, not the token's lifetime. The token is never sent as chat text.

| Command | What happens |
| --- | --- |
| `/token` | Shows whether a token is active and when it was issued. **Issue token** creates one. **Rotate token** replaces it: choose **Rotate and keep delivery** or **Rotate and pause delivery**, then confirm. |
| `/revoke` | Revokes the token without a replacement and pauses delivery until you issue a new token. |

When you rotate, the old token stops working immediately. Webhook deliveries carry signatures for both tokens for one hour, and Mini Apps switch to the new token. After a revocation, Mini Apps stop working until you issue a new token. Follow [Rotate a token](security.md#rotate-a-token) for the complete sequence and [Authentication & tokens](authentication.md) to configure your program.

## Edit the profile <a href="#edit-the-profile" id="edit-the-profile"></a>

| Command | What to send |
| --- | --- |
| `/setname` | A language, then the display name (up to 64 characters). |
| `/setabouttext` | A language, then the about text (up to 120 characters). |
| `/setdescription` | A language, then the description shown in an empty chat (up to 512 characters). |
| `/setuserpic` | A photo, or a square H.264 MP4 video of up to 10 seconds, up to 10 MB. |
| `/setdescriptionpic` | The welcome media shown in an empty chat, with the same format rules. |
| `/setprivacypolicy` | An HTTPS policy URL, or **Use the standard ARX policy**. |

Text limits count UTF-16 code units. Choose **Default**, **English**, **Español** or **العربية** for the language, or type a two-letter language code. `/empty` clears an optional text or removes the media. NexoCreator copies profile media, so deleting your original message does not remove it. See [Commands & profile](commands-and-profile.md) and [Profile media](files.md#profile-media) for the Bot API equivalents.

## Set commands and the menu button <a href="#set-commands-and-the-menu-button" id="set-commands-and-the-menu-button"></a>

`/setcommands` asks for a scope (**Everyone**, **Private chats**, **Groups** or **Group admins**) and a language. Then send one command per line, up to 100 lines:

```text
help - Contact Acme Support
status - Check your order
```

Command names use lowercase letters, digits and underscores, up to 32 characters. Descriptions allow up to 256 characters. NexoCreator shows the current list, marks invalid lines and suggests adding `/start` and `/help`. `/deletecommands` clears one list after you confirm with **Clear command list**.

`/setmenubutton` chooses what the button next to the message field opens: **Default**, **Commands**, or **Mini App** when Mini Apps are available. For a Mini App, send the button text (1 to 64 characters) and the HTTPS address it opens.

Registering a command only adds it to the menu. Implement each command in your program and check permissions there. See [Commands & profile](commands-and-profile.md).

## Configure groups <a href="#configure-groups" id="configure-groups"></a>

| Command | What it controls |
| --- | --- |
| `/setjoingroups` | **Allow groups**: whether people can add your bot to their groups. On by default. |
| `/setprivacy` | **Group privacy**: when on, the bot receives only commands, mentions and replies to its messages in groups. When off, it receives every message. On by default. |
| `/setjoinrequests` | **Join request guard**: your bot reviews requests to join a group through its Main Mini App. Off by default. |
| `/setdefaultadminrights` | **Suggested admin rights**: the rights your bot asks for when someone adds it to a group or a channel. Each chat decides which rights it grants. |
| `/settopics` | **Private chat topics**: topics in private chats with your bot, where available. |
| `/setusertopics` | **Topics created by users**: whether users can create and delete those topics. Turn on private chat topics first. |

Each toggle shows its current state with **Turn on** or **Turn off**. For suggested admin rights, choose **Groups** or **Channels**, select the rights, then **Save** or **Clear**. See [Bots in groups](groups.md) for what your program receives and the operations it can call.

## Set up Mini Apps <a href="#set-up-mini-apps" id="set-up-mini-apps"></a>

These commands appear when Mini Apps are available. Until then they reply "Not available yet."

| Command | What you set |
| --- | --- |
| `/setmainapp` | The Main Mini App: its URL and default size (**Full height**, **Compact** or **Fullscreen**). Its origin is registered for you. |
| `/setsplash` | The launch screen: light and dark background and header colors as `#RRGGBB`, and a 512×512 PNG, JPEG or WebP icon up to 64 KiB. |
| `/setpreviews` | The Main Mini App's preview gallery. |
| `/newapp` | A named Mini App: title, description, a 640×360 JPEG or PNG photo, an optional GIF or MP4 demo (up to 10 MB and 30 seconds), its URL, a permanent short name and default size. |
| `/listapps`, `/editapp`, `/deleteapp` | List, edit or delete named Mini Apps. A deleted short name stays reserved for 30 days. |
| `/setattach` | Offer a Mini App in the attachment menu of your bot's chat, other bot chats or both, and optionally in the side menu. Regular bots need a review; test bots do not. |
| `/setappsettings` | Show **Settings** in the Mini App menu. |
| `/setapporigins` | Add or remove the HTTPS origins your Mini Apps may use. |

A short name uses 3 to 32 lowercase letters, digits or underscores and starts with a letter. You cannot change it later. See [Mini Apps](mini-apps.md) for launching, the client bridge and validating launch data.

## Set up payments <a href="#set-up-payments" id="set-up-payments"></a>

Open the NexoCreator Mini App, choose your bot, then **Payments**. Payments go directly to your wallet.

| Item | What you set |
| --- | --- |
| **Receiving wallet** | Your linked primary wallet by default, or another wallet you own. |
| **Catalogue and products** | Plans: a one-time payment or a subscription, with its price and accepted asset. |
| **Payment settings** | Accepted assets, and whether access to the bot is free or paid. |
| **Payment requirements** | The checklist that must be complete before you can receive payments. |
| **Sales and revenue**, **Subscribers** | Sales, refunds and current subscribers. |

The **Ready to receive payments** checklist covers the receiving wallet, an accepted asset, a payment plan, payment terms, a privacy policy, a payment support contact and acceptance of the ARX merchant terms. Subscriptions run daily, weekly, every 15 days, monthly, yearly or every 1 to 365 days, with a grace period of 0 to 14 days (3 by default). Subscribers approve each renewal in their wallet; nothing renews automatically.

Refunds are transfers you send back from your wallet. You receive the price minus the ARX commission, which is 0 % today, and a refund returns at most the amount you received. A partial refund keeps the buyer's access; a confirmed full refund can end it. Read the same readiness checklist from your program with [`payment_readiness.get`](payments.md), and see [Payments](payments.md) for invoices, checkout updates and subscriptions.

## Turn on advanced modes <a href="#turn-on-advanced-modes" id="turn-on-advanced-modes"></a>

Open **Advanced modes** from your bot's menu. Each mode appears when it is available on the platform, has its own terms, and stays off until you turn it on.

| Mode | What it lets your bot do | Before you turn it on |
| --- | --- | --- |
| **Business connections** | Help people in the chats they choose, on their behalf. | Set a privacy policy, accept the mode terms and declare the data processors you use: the name shown to people, and each processor's name, purpose and privacy policy link. |
| **Manage bots** | Create and manage bots when their owners ask you to. | Set a privacy policy and accept the mode terms. |
| **Guest queries** | Answer requests in chats where your bot is not a member. | Nothing beyond availability. |
| **Bot conversations** | Exchange messages with other bots. | Accept the loop-prevention terms. |

Turning off Business connections disconnects every connected account. Turning off Manage bots stops new creations; bots you already manage stay managed. See [Advanced modes](managed-bots.md) for the API.

## Test access <a href="#test-access" id="test-access"></a>

`/testers` chooses who can use a test bot. Use the sharing button or send public account IDs separated by spaces; new testers are added to the existing list. **Clear** or `/empty` removes everyone. To remove one tester, use the NexoCreator Mini App. A test bot allows up to 50 testers. Removing a tester also removes their access to the bot's protected profile media. See [Testing](testing.md#testbot).

## Delivery and alerts <a href="#delivery-and-alerts" id="delivery-and-alerts"></a>

`/delivery` shows the update mode (polling or webhook, with the webhook host), whether delivery is running or paused, the number of pending updates and the age of the oldest, the maximum connections, the selected IP address, the update filter, updates discarded in the last 24 hours, the last poll and recent errors. It hides the webhook path and query because they can contain secrets.

| Action | What happens |
| --- | --- |
| **Pause delivery** | Stops delivery and keeps queued updates. |
| **Resume delivery** | Restarts delivery when your bot has a current token. |
| **Discard pending updates** | Discards the queue after you confirm. |
| **Remove webhook** | Removes the webhook after you confirm, keeps queued updates and returns to polling. |

A confirmation expires after five minutes. When every webhook attempt returns HTTP 410 for more than 23 hours, ARX removes the webhook, keeps the updates for polling and notifies you. See [Updates](updates.md).

`/alerts` lists health notices such as few replies to private messages or few callback answers. Choose **Fixed**, **Mute 8 hours** or **Mute 1 week**, and **Reactivate** to unmute. See [Alerts](alerts.md). The **Activity** section also links to your bot's statistics in the NexoCreator Mini App.

## Transfer or delete your bot <a href="#transfer-or-delete-your-bot" id="transfer-or-delete-your-bot"></a>

| Command | What happens |
| --- | --- |
| `/transfer` | Offers the bot to another account. Send their ARX ID or choose them with **Choose recipient**; they must have started a chat with the bot you are transferring. Type the phrase `@HANDLE`, then choose **Confirm on this device**. The recipient has 72 hours to accept. |
| `/transfers` | Lists received and sent offers. Accept on your device, reject, or cancel an offer you sent. |
| `/deletebot` | Deletes the bot permanently. Type `Yes, delete @HANDLE`, then confirm on your device. |

A transfer keeps chats and user grants, revokes every credential and stops delivery until the new owner issues a token. Read [Ownership and bot lifecycle](lifecycle.md) before you confirm.

## Developer terms <a href="#developer-terms" id="developer-terms"></a>

NexoCreator shows the developer terms and records your acceptance. If a new version needs acceptance, review the text and its deadline. A change you made before accepting is kept and saved once you accept. Your bot's privacy policy is a separate document that you choose for its users.
