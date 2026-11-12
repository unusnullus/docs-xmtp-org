---
description: "Sell one-time access and manually renewed subscriptions with crypto wallet transfers."
---

# Payments

Your bot can sell one-time purchases and subscriptions. Buyers pay in crypto from their ARX wallet. Each payment is a plain transfer from the buyer's wallet to the receiving wallet you choose in NexoCreator. There is no smart contract and no card processor.

ARX verifies every transfer on chain before it tells your bot that a payment succeeded. Your bot sells, approves each checkout and delivers the purchase. ARX handles the wallet approval, chain verification and access records.

## How a payment works <a href="#how-a-payment-works" id="how-a-payment-works"></a>

1. **Set up payments in NexoCreator.** Choose a receiving wallet, the asset you accept and your plans. Complete the payment requirements. See [Set up payments](payments/setup.md).
2. **Send an invoice.** Your bot sends an invoice message, or creates an invoice link that you can share anywhere. See [Invoices and links](payments/invoices.md).
3. **The buyer reviews it.** The buyer opens the invoice in Messenger, accepts the terms and continues.
4. **Your bot approves the checkout.** You receive a `pre_checkout_query` update and answer it within 10 seconds.
5. **The buyer approves the transfer.** The app wallet sends the exact invoice amount after the buyer confirms with device authentication. If ARX charges a platform commission, it is deducted from that amount and sent as a second transfer in the same checkout. See [Fees](payments/networks.md#fees).
6. **ARX verifies the transfer.** When the transaction is confirmed on chain, your bot receives a `successful_payment` service message.
7. **You deliver.** Fulfill the order or grant access, once per `charge_id`. See [Check and confirm payments](payments/checkout.md).

Approving the checkout does not mean the buyer paid. A transaction hash does not mean the buyer paid. Deliver only after `successful_payment`.

## One-time payments and subscriptions <a href="#one-time-payments-and-subscriptions" id="one-time-payments-and-subscriptions"></a>

A one-time plan grants access after one confirmed payment. Paid access requires the bot's access mode to be Paid; see [Set up payments](payments/setup.md). A subscription plan grants access for a billing period, from 1 to 365 days.

Subscriptions renew manually. Before each period ends, the buyer receives a reminder and approves a new transfer in the app wallet. ARX never charges a wallet in the background, and the buyer never grants a standing spending permission. If the buyer does not renew, access continues through the grace period and then ends. See [Subscriptions and renewals](payments/subscriptions.md).

## What your bot can do <a href="#what-your-bot-can-do" id="what-your-bot-can-do"></a>

| Task | TypeScript | Python and Rust |
| --- | --- | --- |
| Check that checkout is ready | `getPaymentReadiness` | `get_payment_readiness` |
| Read the accepted assets and limits | `getPaymentTokens` | `get_payment_tokens` |
| Read your plans | `getPaymentPlans` | `get_payment_plans` |
| Send an invoice message | `sendInvoice` | `send_invoice` |
| Create an invoice link | `createInvoiceLink` | `create_invoice_link` |
| Approve or reject a checkout | `answerPreCheckoutQuery` | `answer_pre_checkout_query` |
| Read a buyer's access | `getEntitlement` | `get_entitlement` |
| List subscribers | `getSubscriptions` | `get_subscriptions` |
| Request a refund | `refund` | `refund` |
| Read your receiving wallet balance | `getMerchantBalance` | `get_merchant_balance` |
| Read revenue and subscriber reports (not available yet) | `getPaymentAnalytics` | `get_payment_analytics` |

Every payment operation is a server-side Bot API call. Keep your bot token on your server. Never send it to a web page or a Mini App.

## Amounts are base-unit strings <a href="#amounts-are-base-unit-strings" id="amounts-are-base-unit-strings"></a>

Every amount in the payment API is a positive integer string in the asset's smallest unit. Read `decimals` from the token catalog or the invoice, then convert for display. For example, `"5000000"` is 5 USDT when the asset has 6 decimals, and `"5000000000000000000"` is 5 USDT when it has 18.

Never convert amounts through floating point. Never assume a stablecoin has 6 decimals: USDT and USDC on BNB Chain use 18. See [Networks, assets and fees](payments/networks.md).

{% tabs %}
{% tab title="TypeScript" %}

```ts
// Converts "5.25" to base units without floating point.
function toBaseUnits(value: string, decimals: number): string {
  const match = /^(\d+)(?:\.(\d*))?$/.exec(value);
  const whole = match?.[1];
  const fraction = match?.[2] ?? "";
  if (whole === undefined || fraction.length > decimals) {
    throw new Error("Invalid amount");
  }
  const units = BigInt(whole + fraction.padEnd(decimals, "0"));
  if (units <= 0n) throw new Error("Amount must be positive");
  return units.toString();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import re

def to_base_units(value: str, decimals: int) -> str:
    """Converts "5.25" to base units without floating point."""
    match = re.fullmatch(r"([0-9]+)(?:\.([0-9]*))?", value)
    fraction = (match.group(2) or "") if match else ""
    if match is None or len(fraction) > decimals:
        raise ValueError("Invalid amount")
    whole = match.group(1)
    units = int(whole + fraction.ljust(decimals, "0"))
    if units <= 0:
        raise ValueError("Amount must be positive")
    return str(units)
```

{% endtab %}
{% tab title="Rust" %}

```rust
/// Converts "5.25" to base units without floating point.
fn to_base_units(value: &str, decimals: usize) -> Result<String, &'static str> {
    let (whole, fraction) = value.split_once('.').unwrap_or((value, ""));
    let valid = |part: &str| part.bytes().all(|b| b.is_ascii_digit());
    if whole.is_empty() || !valid(whole) || !valid(fraction) || fraction.len() > decimals {
        return Err("Invalid amount");
    }
    let digits = format!("{whole}{fraction:0<decimals$}");
    let trimmed = digits.trim_start_matches('0');
    if trimmed.is_empty() {
        return Err("Amount must be positive");
    }
    Ok(trimmed.to_owned())
}
```

{% endtab %}
{% endtabs %}

## In this section <a href="#in-this-section" id="in-this-section"></a>

- [Set up payments in NexoCreator](payments/setup.md)
- [Invoices and links](payments/invoices.md)
- [Check and confirm payments](payments/checkout.md)
- [Subscriptions and renewals](payments/subscriptions.md)
- [Refunds](payments/refunds.md)
- [Balances and reports](payments/reports.md)
- [Networks, assets and fees](payments/networks.md)
- [Testing payments](payments/testing.md)
- [Payment errors](payments/errors.md)
