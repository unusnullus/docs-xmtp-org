---
description: "Store your bot token and authenticate requests to the Bot API."
---

# Authentication & tokens

Authenticate your program with a bot token issued through NexoCreator. The token belongs to one bot and is separate from the bot owner's ARX account session. It combines a numeric bot ID, a colon and a secret beginning with `aerx_`. Keep the complete token, including its secret suffix, unchanged.

## Get and save a token <a href="#get-and-save-a-token" id="get-and-save-a-token"></a>

Create your bot with `@NEXOCREATOR`, then choose **View token** to open the secure token view in Messenger. Save the token immediately. You can reveal that credential only once. The default reveal window is 60 minutes. This is the time allowed to retrieve it, not the token's lifetime.

If the reveal window ends or you lose your saved token, rotate it in NexoCreator. You cannot recover the same secret by opening the credential view again.

Store the token in a secret manager or an environment variable that your process can read. Keep it out of Git, browser applications, URLs and logs. Anyone who obtains it can act as your bot.

## Authenticate a request <a href="#authenticate-a-request" id="authenticate-a-request"></a>

Send the complete bot token after `Bearer ` in the Authorization header over HTTPS. Never put it in a URL or request body. Set `ARX_BOT_TOKEN` and `ARX_BOT_API_BASE_URL` with the [quickstart setup](quickstart.md#create-the-bot), then read your bot's identity:

```sh
curl --fail-with-body --max-time 30 \
  "$ARX_BOT_API_BASE_URL/me" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

Set `ARX_BOT_API_BASE_URL` to `https://signal.arx.pro/bot-api/v1`. The [SDK connection examples](sdks.md#connect) use the same variables.

## Replace a token safely <a href="#replace-a-token-safely" id="replace-a-token-safely"></a>

If your token may have leaked, revoke it immediately, then issue a new one through NexoCreator. Follow the [Security procedure](security.md#rotate-a-token) to replace the credential in your service and resume delivery.

For a routine replacement, rotate the token through NexoCreator. Replace the stored token in every running process and update your webhook verifier. The [rotation procedure](security.md#rotate-a-token) explains the order and what happens to requests that ARX has already accepted.

## Tokens for bots you manage <a href="#tokens-for-bots-you-manage" id="tokens-for-bots-you-manage"></a>

If your bot has **Manage bots** turned on and creates bots for people, it can read and rotate the token of each bot it manages with [`managed_bots.get_token`](managed-bots.md) and `managed_bots.rotate_token`. Rotation revokes the previous token immediately, and the bot's owner receives a notice. Treat those tokens like your own: never log or share them. See [Advanced modes](managed-bots.md).

## Mini Apps <a href="#mini-apps" id="mini-apps"></a>

A Mini App runs in the user's app. Never send your bot token to it. When your Mini App calls your server, send the signed launch data that ARX gives the Mini App, and verify it on the server before you trust the user it names. Revoking your bot token stops your Mini Apps until you issue a new token. See [Mini Apps](mini-apps.md) for verification.

## What a token cannot do <a href="#what-a-token-cannot-do" id="what-a-token-cannot-do"></a>

A bot token cannot manage the bot owner's account or accept developer terms. Use NexoCreator for those actions. See [Making requests](making-requests.md) for request bodies and failures, or [Receiving updates](receiving-updates.md) to connect your handler.
