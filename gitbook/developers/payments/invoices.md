---
description: "Send an invoice message or create a reusable invoice link for a one-time purchase or a subscription."
---

# Invoices and links

An invoice asks a buyer to pay a fixed amount of one asset. You can send it as a message in a chat with the buyer, or create an invoice link that any ARX user can open.

| Use | Method | Works for |
| --- | --- | --- |
| Invoice message | `sendInvoice` / `send_invoice` | One-time purchases |
| Invoice link | `createInvoiceLink` / `create_invoice_link` | One-time purchases and subscriptions |

A subscription always starts from an invoice link. Sending a subscription plan with `sendInvoice` returns `invalid_request`.

## Invoice fields <a href="#invoice-fields" id="invoice-fields"></a>

| Field | Required | Rules |
| --- | --- | --- |
| `title` | Yes | 1 to 32 characters. |
| `description` | Yes | 1 to 255 characters. |
| `payload` | Yes | 1 to 128 bytes. Your order reference. ARX returns it to your bot at checkout and in the receipt; the buyer does not see it. |
| `asset` | Yes | An asset ID from `getPaymentTokens` that you accept in NexoCreator. |
| `amount` | Yes | The price in base units, as a positive integer string within the asset's `min_amount` and `max_amount`. |
| `plan_id` | No | A plan from `getPaymentPlans`. `asset` and `amount` must equal the plan's `token.asset` and `token.amount`. |

You never send the network, token contract, decimals or receiving address. ARX takes them from the asset catalog and your receiving wallet. Requests that include `chain`, `token`, `recipient`, `decimals`, `invoice_id` or `state` are rejected.

Both methods require an idempotency key of 1 to 64 letters, digits, `_` or `-`. Retrying with the same key and the same body returns the same invoice. The same key with a different body returns `idempotency_key_reused`. Use a new key and a new `payload` for each new order. See [Idempotency](../making-requests.md#idempotency).

## Send an invoice message <a href="#send-an-invoice-message" id="send-an-invoice-message"></a>

`sendInvoice` sends the invoice to a chat with the buyer, the same way `messages.send` sends other content. You can add the usual send options, such as `reply_markup`.

This example reads a one-time plan and sends it as an invoice. Set `CHAT_ID` and `PLAN_ID` first.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const chatId = process.env.CHAT_ID!;
const planId = process.env.PLAN_ID!;
const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const { items } = await bot.getPaymentPlans({ limit: 100 });
  const plan = items.find((item) => item.plan_id === planId && item.kind === "one_time");
  if (!plan) throw new Error("One-time plan not found");

  const orderId = "order-123"; // Your own order reference, stored before sending.
  const sent = await bot.sendInvoice(
    chatId,
    {
      title: "Pro access",
      description: "Lifetime access to Pro commands",
      payload: orderId,
      asset: plan.token.asset,
      amount: plan.token.amount,
      plan_id: plan.plan_id,
    },
    { key: `invoice-${orderId}` },
  );
  console.log(sent.message_id);
} finally {
  await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models

chat_id = os.environ["CHAT_ID"]
plan_id = os.environ["PLAN_ID"]

with Client(Config(base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    page = bot.get_payment_plans(models.PaymentPlansListQuery(limit=100))
    plan = next(
        (item.root for item in page.items if item.root.plan_id == plan_id and item.root.kind == "one_time"),
        None,
    )
    if plan is None:
        raise SystemExit("One-time plan not found")

    order_id = "order-123"  # Your own order reference, stored before sending.
    sent = bot.send_invoice(
        chat_id,
        models.InputInvoice(
            type="invoice",
            title="Pro access",
            description="Lifetime access to Pro commands",
            payload=order_id,
            asset=plan.token.asset,
            amount=plan.token.amount,
            plan_id=plan.plan_id,
        ),
        key=f"invoice-{order_id}",
    )
    print(sent.message_id)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let chat_id = std::env::var("CHAT_ID")?;
    let plan_id = std::env::var("PLAN_ID")?;
    let config = Config::new(std::env::var("ARX_BOT_TOKEN")?, "https://signal.arx.pro/bot-api/v1", false)?;
    let bot = Client::new(config)?;

    let query = models::PaymentPlansListQuery { limit: Some(100), ..Default::default() };
    let page = bot.get_payment_plans(&query).await?;
    let token = page
        .items
        .iter()
        .find_map(|item| match item {
            models::PaymentPlan::PaymentOneTimePlan(plan) if plan.plan_id == plan_id => {
                Some(plan.token.clone())
            }
            _ => None,
        })
        .ok_or("One-time plan not found")?;

    let order_id = "order-123"; // Your own order reference, stored before sending.
    let mut invoice = models::InputInvoice::new(
        "Pro access".into(),
        "Lifetime access to Pro commands".into(),
        order_id.into(),
        token.asset,
        token.amount,
    );
    invoice.plan_id = Some(plan_id);
    let key = format!("invoice-{order_id}");
    let sent = bot.send_invoice(&chat_id, &invoice, Some(&key)).await?;
    println!("{}", sent.message_id());
    Ok(())
}
```

{% endtab %}
{% endtabs %}

You can also send an invoice without a plan. Supply any accepted `asset` and an `amount` within its limits. A one-time purchase without a plan works the same way at checkout.

### The sent invoice <a href="#the-sent-invoice" id="the-sent-invoice"></a>

The sent message has `content.type` set to `invoice`. ARX adds the transfer details the buyer's wallet uses:

```json
{
  "type": "invoice",
  "title": "Pro access",
  "description": "Lifetime access to Pro commands",
  "asset": "eip155:56/erc20:0x55d398326f99059ff775485246999027b3197955",
  "amount": "5000000000000000000",
  "invoice_id": "INVOICE_ID",
  "chain": "eip155:56",
  "token": "0x55d398326f99059ff775485246999027b3197955",
  "decimals": 18,
  "recipient": "YOUR_RECEIVING_WALLET_ADDRESS"
}
```

`token` is the contract address, or `null` for the network's native coin. `recipient` is your receiving wallet for that network, or your linked main wallet if you have not chosen one. Read `decimals` from this object or the catalog instead of assuming a value.

## Create an invoice link <a href="#create-an-invoice-link" id="create-an-invoice-link"></a>

`createInvoiceLink` returns:

| Field | Meaning |
| --- | --- |
| `url` | The link to share, in the form `https://www.arx.pro/invoice/<slug>`. |
| `slug` | The link's identifier. |
| `invoice_id` | The invoice behind the link. |

Many buyers can pay through one link. Each buyer gets their own purchase, and for a subscription plan, their own subscription. Opening the same link again resumes that buyer's purchase instead of starting a second one.

This example creates a link for a subscription plan.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const planId = process.env.PLAN_ID!;
const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const { items } = await bot.getPaymentPlans({ limit: 100 });
  const plan = items.find((item) => item.plan_id === planId && item.kind === "recurring");
  if (!plan) throw new Error("Subscription plan not found");

  const link = await bot.createInvoiceLink(
    {
      title: "Pro monthly",
      description: "Pro commands, renewed each month",
      payload: `plan-${plan.plan_id}`,
      asset: plan.token.asset,
      amount: plan.token.amount,
      plan_id: plan.plan_id,
    },
    { key: `link-${plan.plan_id}-v${plan.version}` },
  );
  console.log(link.url);
} finally {
  await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models

plan_id = os.environ["PLAN_ID"]

with Client(Config(base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    page = bot.get_payment_plans(models.PaymentPlansListQuery(limit=100))
    plan = next(
        (item.root for item in page.items if item.root.plan_id == plan_id and item.root.kind == "recurring"),
        None,
    )
    if plan is None:
        raise SystemExit("Subscription plan not found")

    link = bot.create_invoice_link(
        models.CreateInvoiceLink(
            title="Pro monthly",
            description="Pro commands, renewed each month",
            payload=f"plan-{plan.plan_id}",
            asset=plan.token.asset,
            amount=plan.token.amount,
            plan_id=plan.plan_id,
        ),
        key=f"link-{plan.plan_id}-v{plan.version}",
    )
    print(link.url)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let plan_id = std::env::var("PLAN_ID")?;
    let config = Config::new(std::env::var("ARX_BOT_TOKEN")?, "https://signal.arx.pro/bot-api/v1", false)?;
    let bot = Client::new(config)?;

    let query = models::PaymentPlansListQuery { limit: Some(100), ..Default::default() };
    let page = bot.get_payment_plans(&query).await?;
    let plan = page
        .items
        .iter()
        .find_map(|item| match item {
            models::PaymentPlan::PaymentRecurringPlan(plan) if plan.plan_id == plan_id => {
                Some(plan.clone())
            }
            _ => None,
        })
        .ok_or("Subscription plan not found")?;

    let mut request = models::CreateInvoiceLink::new(
        "Pro monthly".into(),
        "Pro commands, renewed each month".into(),
        format!("plan-{}", plan.plan_id),
        plan.token.asset.clone(),
        plan.token.amount.clone(),
    );
    request.plan_id = Some(plan.plan_id.clone());
    let key = format!("link-{}-v{}", plan.plan_id, plan.version);
    let link = bot.create_invoice_link(&request, Some(&key)).await?;
    println!("{}", link.url);
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The key includes the plan version, so the link keeps the price you read. After you change the plan's price, create a new link for new buyers.

## What the buyer sees <a href="#what-the-buyer-sees" id="what-the-buyer-sees"></a>

The buyer opens the invoice in Messenger, reviews it, accepts your payment terms and continues. After your bot approves the checkout, the buyer confirms the transfer in the app wallet with device authentication. The wallet pays the network fee separately; it is never taken from the invoice amount.

The owner of a bot cannot pay that bot's invoices. Use a second ARX account to test. See [Testing payments](testing.md).

## Next step <a href="#next-step" id="next-step"></a>

Handle the checkout and the confirmed payment in [Check and confirm payments](checkout.md).
