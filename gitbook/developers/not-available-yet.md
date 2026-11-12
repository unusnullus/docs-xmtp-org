---
description: "Check the platform features that a Nexo bot cannot use today."
---

# Not available yet

Check this list before you design your bot around one of these features. Each item is unavailable for every bot today. Where the API already defines a related field or operation, using it returns `capability_not_available` unless the item says otherwise. Choose a supported alternative from the [capability map](features.md).

## Messages and buttons <a href="#messages-and-buttons" id="messages-and-buttons"></a>

- Inline mode is not available. You cannot turn on `supports_inline_queries` or `supports_inline_location`, so your bot receives no `inline_query` or `chosen_inline_result` updates and cannot use `inline_queries.answer`.
- Switch-inline, login, game and pay buttons are not available. Inline buttons support `callback`, `url`, `copy_text`, `mini_app` and `disabled`.
- Games are not available, including game results and links that open a game.
- Custom emoji are not available in messages or reactions.
- Country-restricted polls are not available. Omit `country_codes` from polls.
- Stories are not available. Story administrator rights must be `false` or omitted.
- You can send stickers, but you cannot create, edit or read sticker sets.

## Groups and channels <a href="#groups-and-channels" id="groups-and-channels"></a>

- Your bot cannot change a group's or channel's title or description.
- In a channel, your bot can post text and rich text only. Media, user mentions and other content types return `capability_not_available`.

## Payments <a href="#payments" id="payments"></a>

- Paid media and paid broadcasts are not available.
- Payments use crypto assets only. There is no in-app currency, and gifts and chat boosts are not available.
- There is no test network or sandbox for payments. Payments move real funds on public networks.
- Shipping is not available. Invoices cannot collect a shipping address or offer shipping options, and your bot receives no `shipping_query` updates.
- Extending a subscription from your bot (`subscriptions.extend`) and the merchant transaction history (`merchant_transactions.list`) are not available. Both return `capability_not_available`. Renewals need a new wallet approval; follow payments with `subscriptions.list` and payment updates.
- Daily payment analytics (`payment_analytics.get`) are not available. The call returns HTTP 503 `upstream_unavailable`.
- No update reports refund progress. See [Refunds](payments/refunds.md#effect-on-access) for how to read the current result.

## Mini Apps <a href="#mini-apps" id="mini-apps"></a>

- A Mini App cannot request a wallet signature. To take a payment from a Mini App, create an [invoice link](payments/invoices.md#create-an-invoice-link) and open it with `openInvoice`; see [Payments](payments.md).
- A Mini App cannot set the user's emoji status, share to a story, run age verification or restore a SecureStorage value.
- A result your bot sends for a Mini App (`mini_app_queries.answer`, `prepared_messages.save`) or a guest query cannot be a game or sticker, share a live location or carry callback buttons.

## Business connections <a href="#business-connections" id="business-connections"></a>

- The Business endpoint report operations (`business_connection.endpoint_report.get` and `business_connection.endpoint_report.set`) are not available. They return HTTP 503 `upstream_unavailable`.

## Links <a href="#links" id="links"></a>

- Bot links open from inside ARX Messenger. Opening a bot link from a web page straight into the app, or keeping a link through app installation, is not available.

## Platform <a href="#platform" id="platform"></a>

- A self-hosted Nexo Bot API server is not available.

Use the [API reference](api.md) for the operations you can call, and the [changelog](changes.md) to follow public changes.
