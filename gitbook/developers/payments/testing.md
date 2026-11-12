---
description: "Test your payment handlers locally, then run a real purchase with a second account."
---

# Testing payments

Test payment handling in two steps: run your handlers against synthetic updates, then make a small real purchase from a second ARX account.

## Test handlers with synthetic updates <a href="#test-handlers-with-synthetic-updates" id="test-handlers-with-synthetic-updates"></a>

The [local emulator](../testing.md) does not serve payment operations. Test your payment logic by passing synthetic update objects to your handler, shaped like the updates in [Check and confirm payments](checkout.md). Cover these cases:

- A `pre_checkout_query` for a known order with the right amount and buyer. Your bot approves it.
- A `pre_checkout_query` with a wrong amount, an unknown `invoice_payload` or another buyer. Your bot rejects it with an `error_message`.
- A `successful_payment` for a new `charge_id`. Your bot delivers once.
- The same `successful_payment` delivered twice. Your bot does not deliver again.
- A second `successful_payment` with a new `charge_id` for an order you already delivered. Your bot does not deliver again.
- A `payment_reverted` for a delivered charge. Your bot withdraws what it granted.
- A `subscription_updated` with `state: "expired"` and no `invoice_payload`. Your bot handles it by `subscription_id`.
- A process restart between saving a result and acknowledging the update. Your bot does not repeat the work.

Mock the HTTP layer to test calls such as `answerPreCheckoutQuery` and `refund` without sending requests.

## Run a real purchase <a href="#run-a-real-purchase" id="run-a-real-purchase"></a>

Payments move real funds on public networks. There is no test network or sandbox mode. Use small amounts on a network with low fees.

1. Set up payments for your bot in NexoCreator, set its access mode to Paid, and confirm that `getPaymentReadiness` returns `ready: true`. See [Set up payments](setup.md).
2. Use a second ARX account as the buyer. A bot owner cannot pay their own bot's invoices. Fund the buyer's wallet with the asset and with the network's native coin for the network fee.
3. From the buyer account, start a chat with your bot. Send an invoice, or open an invoice link.
4. Continue from the invoice. Check that your bot receives `pre_checkout_query` and answers within 10 seconds.
5. Approve the transfer in the buyer's wallet. After confirmation, check that your bot receives `successful_payment` and that `getEntitlement` returns `has_access: true`.
6. Request a refund with `refund`, then send it from NexoCreator. Check the buyer's access again.

For a subscription, also check the renewal path. Create a daily plan so the period ends within a day, add `renewal_due` to your update filter, and renew from the buyer account when the reminder arrives.

Before you launch, work through the [go-live checklist](../go-live-checklist.md).
