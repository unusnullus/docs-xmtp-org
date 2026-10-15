---
description: "Understand what a bot receives and what blocking or deleting a chat changes."
---

# Privacy in bot chats

You can decide what to share with a bot and control whether it can keep interacting with you. Bot chats are not encrypted end to end: ARX processes your messages, and the bot's developer can read and store the content delivered to the bot.

Starting a bot does not give it access to your other conversations or their encryption keys. Network encryption protects data in transit, but it does not prevent the bot's developer from reading what you send to that bot.

## What a bot can receive <a href="#what-a-bot-can-receive" id="what-a-bot-can-receive"></a>

| You share or make available | The Bot API does not supply |
| --- | --- |
| Your user ID, name, available handle and app language. | Your other conversations, encryption keys, device IDs or IP address. |
| Messages, files, commands and button presses in the bot chat. | Your phone number or wallet address automatically. |
| Contacts and locations you choose to send. | Access to other ARX chats. |

Photos, audio, video, documents and contact cards can contain personal information. The developer can read content you copy into the bot chat, including material from another conversation. Share only what the service needs.

## Read the bot's privacy policy <a href="#read-the-bots-privacy-policy" id="read-the-bots-privacy-policy"></a>

Open Privacy policy in the bot's profile before sending sensitive information. A bot may link its own policy or adopt the standard ARX bot policy. You can also try `/privacy` or `/help` if the bot supports them.

Check what the developer collects, why it is needed, how long it is retained, which providers receive it and how to contact the developer. Some bots send content to external services, such as AI providers. [Official bots](official-bots.md) describes the data used by ARX's official bots.

## Block a bot <a href="#block-a-bot" id="block-a-bot"></a>

Choose Block in the profile or chat controls to stop new interactions and bot messages. You can still read or delete your history and report abuse.

An interaction accepted before the block may still reach the bot. Blocking cannot recall data already delivered or erase copies held by the developer or its providers. Unblock or Start allows interactions again and sends a new `/start`.

## Delete messages or clear history <a href="#delete-messages-or-clear-history" id="delete-messages-or-clear-history"></a>

You can delete messages you have permission to remove or clear the chat history. ARX removes affected content and file references through background cleanup. That does not immediately erase every file copy.

Clearing history does not block the bot. Choose Block separately if you also want to stop interactions. Neither clearing nor deletion removes copies that the developer or external providers already hold.

Clearing history does not send the developer a data-deletion request. Contact the developer through the privacy policy or help information to request deletion of their copies.

## Report abuse <a href="#report-abuse" id="report-abuse"></a>

Report from the profile or chat controls and select the evidence you want ARX to review. ARX stores the selected evidence separately for authorized moderation, so later edits or clearing the chat do not remove that evidence. The bot owner does not receive your report, evidence or reporter identity.

A report and a data-deletion request serve different purposes. For help with a bot, start with its privacy policy or support information. For ARX platform issues, see [Support](support.md).
