---
description: "Approve checkouts within 10 seconds, deliver after successful_payment and handle duplicates and reversals."
---

# Check and confirm payments

Your bot takes part in each payment twice. It approves the checkout before the buyer sends money, and it delivers the purchase after ARX confirms the transfer on chain.

| Update | When | What to do |
| --- | --- | --- |
| `pre_checkout_query` | The buyer continues from the invoice, before any transfer. | Check the order and answer within 10 seconds. |
| `message` with service `successful_payment` | ARX confirmed the transfer on chain. | Deliver once per `charge_id`. |
| `message` with service `payment_reverted` | A confirmed transfer was removed by a chain reorganization. | Withdraw what that charge granted. |

## Approve the checkout <a href="#approve-the-checkout" id="approve-the-checkout"></a>

When the buyer continues from an invoice, your bot receives a `pre_checkout_query` update. It contains:

| Field | Meaning |
| --- | --- |
| `id` | The query to answer. |
| `user` | The buyer. |
| `invoice_payload` | The `payload` you set on the invoice. |
| `chain` | The network, for example `eip155:56`. |
| `token` | The token contract, or `null` for the native coin. |
| `amount` | The exact price in base units. |
| `decimals` | The asset's decimals. |
| `recipient` | Your receiving wallet address. |
| `expires_at` | The answer deadline, 10 seconds after the query was created. |

Look up the order by `invoice_payload`. Check that it is still available and that `amount` and the buyer match what you stored. Then call `answerPreCheckoutQuery` (`answer_pre_checkout_query`):

- To approve, send `ok: true` without `error_message`.
- To reject, send `ok: false` with an `error_message` of 1 to 256 characters.

If you do not answer before `expires_at`, the checkout does not proceed. An answer after the deadline, or a second answer, returns `query_expired`. An unknown query ID also returns `query_expired`.

Approval lets the buyer send the transfer. It is not a payment. Do not deliver anything, reserve stock you cannot release, or call slow services before you answer.

If your bot uses polling, keep the poll loop short so checkouts do not wait behind other updates. The SDK pollers process the updates with the earliest deadline first within each batch, so a `pre_checkout_query` runs before ordinary messages.

## Deliver after `successful_payment` <a href="#deliver-after-successful_payment" id="deliver-after-successful_payment"></a>

When the transfer is confirmed on chain, your bot receives a `message` update in the buyer's chat. The message has `kind: "service"` and `service.type` set to `successful_payment`:

| Field | Meaning |
| --- | --- |
| `charge_id` | The confirmed charge. Use it as your fulfillment key. |
| `invoice_payload` | Your order reference. |
| `chain` | The network. |
| `token` | `asset`, `amount`, `decimals` and `contract` (`null` for the native coin). `amount` is what reached your receiving wallet: the invoice price minus any [platform commission](networks.md#fees). Match the order by `invoice_payload`, not by this amount. |
| `tx_hash` | The confirmed transaction. |

Deliver only now. Save the `charge_id` together with the delivery result in one transaction, and skip any `charge_id` you already processed. Updates can arrive more than once.

A buyer can pay twice for the same purchase. The second payment has its own `charge_id` and the same `invoice_payload`. If you already delivered that order, do not deliver again. Consider [refunding](refunds.md) the extra charge. A `duplicate_of_charge_id` field that names the first charge is coming soon to the typed SDK models; until then, detect a second payment from your own order records, as the example below does.

For access-based products, you can also read the buyer's current access with `getEntitlement`. See [Check access](subscriptions.md#check-access).

## Example handler <a href="#example-handler" id="example-handler"></a>

This handler approves known orders and delivers each confirmed charge once. It keeps orders in memory to stay short; store orders and processed charges in your database.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

type Order = { buyerId: string; amount: string; delivered: boolean };
const orders = new Map<string, Order>(); // Keyed by invoice payload.
const processedCharges = new Set<string>();

export async function handlePayment(bot: Client, update: Models.Update): Promise<void> {
  const query = update.pre_checkout_query;
  if (query) {
    const order = orders.get(query.invoice_payload);
    const valid =
      order !== undefined &&
      !order.delivered &&
      order.amount === query.amount &&
      order.buyerId === query.user.id;
    await bot.answerPreCheckoutQuery(
      query.id,
      valid ? { ok: true } : { ok: false, error_message: "This order is no longer available." },
    );
    return;
  }

  const message = update.message;
  if (!message || message.kind !== "service") return;
  const service = message.service;
  if (service.type === "successful_payment") {
    if (processedCharges.has(service.charge_id)) return;
    processedCharges.add(service.charge_id);
    const order = orders.get(service.invoice_payload);
    if (!order) return;
    if (order.delivered) return; // Paid twice; review service.charge_id for a refund.
    order.delivered = true; // Deliver the purchase here.
  } else if (service.type === "payment_reverted") {
    // Withdraw what service.charge_id granted.
  }
}
```

{% endtab %}
{% tab title="Python" %}

```python
from dataclasses import dataclass

from arx_nexo import Client, models

@dataclass
class Order:
    buyer_id: str
    amount: str
    delivered: bool = False

orders: dict[str, Order] = {}  # Keyed by invoice payload.
processed_charges: set[str] = set()

def handle_payment(bot: Client, update: models.Update) -> None:
    query = update.pre_checkout_query
    if query is not None:
        order = orders.get(query.invoice_payload)
        valid = (
            order is not None
            and not order.delivered
            and order.amount == query.amount
            and order.buyer_id == query.user.id
        )
        answer = (
            models.PreCheckoutAnswer(ok=True)
            if valid
            else models.PreCheckoutAnswer(ok=False, error_message="This order is no longer available.")
        )
        bot.answer_pre_checkout_query(query.id, answer)
        return

    message = update.message
    service = message.service if message is not None else None
    event = service.root if service is not None else None
    if isinstance(event, models.SuccessfulPayment):
        if event.charge_id in processed_charges:
            return
        processed_charges.add(event.charge_id)
        order = orders.get(event.invoice_payload)
        if order is None:
            return
        if order.delivered:
            return  # Paid twice; review event.charge_id for a refund.
        order.delivered = True  # Deliver the purchase here.
    elif isinstance(event, models.PaymentReverted):
        pass  # Withdraw what event.charge_id granted.
```

{% endtab %}
{% tab title="Rust" %}

```rust
use std::collections::{HashMap, HashSet};

use arx_nexo::models::{IncomingMessage, PreCheckoutAnswer, Service, Update};
use arx_nexo::{Client, Error};

pub struct Order {
    pub buyer_id: String,
    pub amount: String,
    pub delivered: bool,
}

#[derive(Default)]
pub struct Payments {
    pub orders: HashMap<String, Order>, // Keyed by invoice payload.
    pub processed_charges: HashSet<String>,
}

impl Payments {
    pub async fn handle(&mut self, bot: &Client, update: &Update) -> Result<(), Error> {
        if let Some(query) = update.pre_checkout_query() {
            let valid = self.orders.get(&query.invoice_payload).is_some_and(|order| {
                !order.delivered && order.amount == query.amount && order.buyer_id == query.user.id
            });
            let mut answer = PreCheckoutAnswer::new(valid);
            if !valid {
                answer.error_message = Some("This order is no longer available.".into());
            }
            bot.answer_pre_checkout_query(&query.id, &answer, None).await?;
            return Ok(());
        }

        let Some(IncomingMessage::Message(message)) = update.message() else {
            return Ok(());
        };
        match message.service() {
            Some(Service::SuccessfulPaymentService(payment)) => {
                if !self.processed_charges.insert(payment.charge_id.clone()) {
                    return Ok(());
                }
                if let Some(order) = self.orders.get_mut(&payment.invoice_payload) {
                    if order.delivered {
                        return Ok(()); // Paid twice; review payment.charge_id for a refund.
                    }
                    order.delivered = true; // Deliver the purchase here.
                }
            }
            Some(Service::PaymentRevertedService(_reverted)) => {
                // Withdraw what reverted.charge_id granted.
            }
            _ => {}
        }
        Ok(())
    }
}
```

{% endtab %}
{% endtabs %}

Call the handler from your [polling loop or webhook receiver](../updates.md). Advance the polling offset, or acknowledge the webhook, only after the handler has saved its result.

## Handle a reversal <a href="#handle-a-reversal" id="handle-a-reversal"></a>

In rare cases a chain reorganization removes a transaction after ARX confirmed it. Your bot then receives a `payment_reverted` service message with the same `charge_id`. It carries only the `charge_id`, so look up what you delivered by the `charge_id` you saved. ARX removes the access that charge granted.

Reverse what you delivered for that charge where you can. Before you allow paid features again, read `getEntitlement`.

## Payment sequence numbers <a href="#payment-sequence-numbers" id="payment-sequence-numbers"></a>

Payment updates can carry `pay_seq` and `prev_pay_seq`. They are decimal strings that order payment events for one buyer of your bot. The first `prev_pay_seq` can be `"0"`. Keep them as strings; they can exceed the safe integer range in JavaScript. They do not replace `update_id` for polling offsets and do not prove a payment.

## What is not proof of payment <a href="#what-is-not-proof-of-payment" id="what-is-not-proof-of-payment"></a>

- An approved `pre_checkout_query`.
- A transaction hash from any source other than `successful_payment`.
- A `renewal_due` reminder or a `subscription_updated` update.
- A wallet balance from `getMerchantBalance`.

Only `successful_payment` and the access returned by `getEntitlement` reflect confirmed payments.
