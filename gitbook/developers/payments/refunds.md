---
description: "Request a full or partial refund from your bot, then send it from your wallet in NexoCreator."
---

# Refunds

A refund is a transfer from the bot owner's wallet back to the buyer. Your bot can request one. The bot owner then sends it from the app wallet in NexoCreator. A bot token can never move money.

## How a refund works <a href="#how-a-refund-works" id="how-a-refund-works"></a>

1. Your bot calls `refund` with the `charge_id` from `successful_payment` and the buyer's user ID.
2. ARX reserves the amount and returns a refund request in the `awaiting_merchant_transfer` state, with HTTP 202.
3. The bot owner opens **Payments** › **Sales and revenue** in NexoCreator, selects the sale and continues in the wallet. The app asks for device authentication and sends the refund from the owner's linked main wallet.
4. ARX verifies the refund transfer on chain.

The refund goes to the wallet that paid, in the same asset and on the same network as the original payment. You cannot choose another recipient, asset or network. The owner's wallet pays the network fee for the refund.

The owner can also start a refund directly from the sale in NexoCreator, without the Bot API.

## Request a refund <a href="#request-a-refund" id="request-a-refund"></a>

| Field | Required | Rules |
| --- | --- | --- |
| `user_id` | Yes | The buyer's user ID from the payment. |
| `amount` | No | A partial amount in base units. Omit it to refund everything that is still refundable. It cannot exceed the amount your wallet received for the charge (`token.amount` in `successful_payment`), minus earlier refunds. A platform commission, when charged, is not refunded. |

Send an idempotency key of 1 to 64 letters, digits, `_` or `-`. Save the key and the request body before you call the API, and reuse both on every retry, including after a restart. A retry returns the original receipt, even after the request has expired. Do not compute a new amount for a retry.

Set `REFUND_KEY` to a fresh 1–64 character idempotency key and persist it with the refund body before the first request.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const chargeId = process.env.CHARGE_ID!;
const buyerId = process.env.USER_ID!;
const refundKey = process.env.REFUND_KEY!; // Save this before calling the API.

const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const request = await bot.refund(chargeId, { user_id: buyerId }, { key: refundKey });
  console.log(request.refund_id, request.state, request.expires_at);
} finally {
  await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models

charge_id = os.environ["CHARGE_ID"]
buyer_id = os.environ["USER_ID"]
refund_key = os.environ["REFUND_KEY"]  # Save this before calling the API.

with Client(Config(base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    request = bot.refund(charge_id, models.RefundCharge(user_id=buyer_id), key=refund_key)
    print(request.refund_id, request.state, request.expires_at)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let charge_id = std::env::var("CHARGE_ID")?;
    let buyer_id = std::env::var("USER_ID")?;
    let refund_key = std::env::var("REFUND_KEY")?; // Save this before calling the API.
    let config = Config::new(std::env::var("ARX_BOT_TOKEN")?, "https://signal.arx.pro/bot-api/v1", false)?;
    let bot = Client::new(config)?;

    let body = models::RefundCharge::new(buyer_id);
    let request = bot.refund(&charge_id, &body, Some(&refund_key)).await?;
    println!("{} {:?} {}", request.refund_id, request.state, request.expires_at);
    Ok(())
}
```

{% endtab %}
{% endtabs %}

These examples request a full refund. For a partial refund, add `amount`, for example `{ user_id: buyerId, amount: "1000000" }`.

The response contains only `refund_id`, `state` (`awaiting_merchant_transfer`) and `expires_at`. It is a request, not a completed refund: no money has moved yet.

## Rules <a href="#rules" id="rules"></a>

- Only a confirmed charge can be refunded.
- The buyer must still have a chat with your bot.
- A request expires 7 days after it is created if the owner does not send it.
- Several partial refunds can be made against one charge, up to the amount your wallet received.

## Effect on access <a href="#effect-on-access" id="effect-on-access"></a>

A partial refund does not change access. A confirmed full refund removes the access that the charge granted. When every paid period of a subscription is refunded, the subscription becomes `refunded`.

There is no dedicated refund-confirmed update. A refund that changes a subscription's access deadline or state can emit `subscription_updated`. Read [`getEntitlement`](subscriptions.md#check-access) and `getSubscriptions` for the current result. The `refund_request_updated` type in the default update filter is reserved and sends nothing today.

## Duplicate payments <a href="#duplicate-payments" id="duplicate-payments"></a>

If a buyer pays twice for the same purchase, you receive a second `successful_payment` with a new `charge_id` and the same `invoice_payload`. Refund that second `charge_id` to return the extra payment. A `duplicate_of_charge_id` field that names the first charge is coming soon to the typed SDK models.
