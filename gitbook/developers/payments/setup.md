---
description: "Choose a receiving wallet, accepted asset and plans in NexoCreator, then check that checkout is ready."
---

# Set up payments

The bot owner sets up payments in NexoCreator. Your code then reads the setup through the Bot API: whether checkout is ready, which assets the bot accepts and which plans it sells.

## Configure payments in NexoCreator <a href="#configure-payments-in-nexocreator" id="configure-payments-in-nexocreator"></a>

Open `@NEXOCREATOR`, choose your bot and choose **Payments**.

1. **Receiving wallet.** Payments go to your linked main wallet by default. Choose **Change** to select another of your EVM wallets, or create a new one, then confirm. A new wallet is not selected until you choose it.
2. **Payment settings.** Choose the network and asset that buyers pay with.
3. **Catalogue and products.** Choose **Create plan**. Pick **One-time payment** or **Subscription**, then the network, asset and price. A subscription also needs a billing period and a grace period.
4. **Payment requirements.** Add your payment terms, either as text of up to 4,096 characters or as an HTTPS link. Add a support contact of up to 256 characters. Read and accept the ARX merchant terms.

To grant paid access, set the bot's access mode to Paid before checkout. A one-time payment made while the bot is Free does not grant paid access. Subscriptions require Paid mode.

Subscription billing periods:

| Period | Length |
| --- | --- |
| Daily | 1 day |
| Weekly | 7 days |
| Every 15 days | 15 days |
| Monthly | 30 days |
| Yearly | 365 days |
| Custom | 1 to 365 days |

The grace period is 0 to 14 days. The default is 3 days. During grace the subscriber keeps access while they renew. See [Subscriptions and renewals](subscriptions.md).

A plan's price must stay within the asset's minimum and maximum amounts. NexoCreator assigns each plan a `plan_id`; read it with `getPaymentPlans`.

### Change a plan's price <a href="#change-a-plans-price" id="change-a-plans-price"></a>

**Update plan** changes only the price. The type, asset, billing period and grace period stay fixed. Each update creates a new plan version. A buyer's first period uses the version on their invoice. Every renewal uses the plan's current price, which the subscriber sees before approving the transfer.

To sell different terms, create another plan.

## Prepare your bot to receive checkouts <a href="#prepare-your-bot-to-receive-checkouts" id="prepare-your-bot-to-receive-checkouts"></a>

Your bot must receive updates through [polling or a webhook](../updates.md), and its update filter must include `pre_checkout_query`. The default filter includes it. If you set `allowed_updates` explicitly, include `default` or `pre_checkout_query`.

## Check readiness <a href="#check-readiness" id="check-readiness"></a>

Call `getPaymentReadiness` (`get_payment_readiness` in Python and Rust). It returns the current setup verdict. A bot that is not ready is a successful response with `ready: false`, so handle request errors separately. If no accepted asset is set up in NexoCreator yet, the call returns `upstream_unavailable` instead of `ready: false`. Choose an asset in **Payment settings** first.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const setup = await bot.getPaymentReadiness();
  if (setup.ready) {
    console.log("Checkout is ready");
  } else {
    console.log(`Not ready (${setup.reason}): ${setup.missing.join(", ")}`);
  }
} finally {
  await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
from arx_nexo import Client, Config

with Client(Config(base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    setup = bot.get_payment_readiness()
    if setup.ready:
        print("Checkout is ready")
    else:
        print(f"Not ready ({setup.reason}): {', '.join(setup.missing)}")
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let config = Config::new(std::env::var("ARX_BOT_TOKEN")?, "https://signal.arx.pro/bot-api/v1", false)?;
    let bot = Client::new(config)?;
    let setup = bot.get_payment_readiness().await?;
    if setup.ready {
        println!("Checkout is ready");
    } else {
        println!("Not ready ({:?}): {}", setup.reason, setup.missing.join(", "));
    }
    Ok(())
}
```

{% endtab %}
{% endtabs %}

`reason` is `payout_address_missing` when a receiving wallet is missing for a network, otherwise `checklist_incomplete`. It is omitted when `ready` is `true`.

Each entry in `missing` names one item to fix:

| Item | Fix |
| --- | --- |
| `terms` | Add payment terms in **Payment requirements**. |
| `support_contact` | Add a support contact in **Payment requirements**. |
| `merchant_terms` | Accept the current ARX merchant terms. Acceptance resets when the bot changes owner. |
| `delivery_mode` | Start polling or set a webhook. |
| `allowed_updates:pre_checkout_query` | Add `pre_checkout_query` to your update filter. |
| `allowed_updates:shipping_query` | Reserved. Shipping is [not available yet](../not-available-yet.md), so this item does not appear today. |
| `payout_address:<chain>` | Set a receiving wallet for that network, for example `payout_address:eip155:56`. |

The response also reports:

- `delivery_mode`: `none`, `polling`, `webhook` or `internal`.
- `payment_updates_allowed`: booleans for `pre_checkout_query`, `shipping_query`, `purchased_paid_media` and `subscription_updated` in your filter.
- `payout_addresses`: one entry per network with `chain`, `verified` and `registered`. A network can receive payments when both are `true`.
- `polling_backlog`: the number of pending updates, present only when you use polling. More than 1,000 means your bot is falling behind; checkout updates expire in 10 seconds.

Readiness reports configuration. It does not confirm a payment or grant access. Read it again after you change the setup.

## Read the accepted assets <a href="#read-the-accepted-assets" id="read-the-accepted-assets"></a>

Call `getPaymentTokens` (`get_payment_tokens`) to read the payment asset catalog. Each token has:

| Field | Meaning |
| --- | --- |
| `asset` | The asset ID to put in invoices, for example `eip155:56/erc20:0x55d398326f99059ff775485246999027b3197955`. |
| `chain` | The network, for example `eip155:56`. |
| `symbol` | A display label such as `USDT`. Several networks share a symbol, so never select an asset by symbol. |
| `contract` | The lowercase token contract address, or `null` for the network's native coin. |
| `decimals` | The number of decimals for amounts in this asset. |
| `min_amount`, `max_amount` | Inclusive price limits in base units. |

The catalog lists every asset ARX supports. Your bot can invoice only in the assets you accepted in NexoCreator. Select by `asset`, and keep `min_amount` and `max_amount` as strings; the maximum can be as large as 2<sup>256</sup> − 1.

## Read your plans <a href="#read-your-plans" id="read-your-plans"></a>

Call `getPaymentPlans` (`get_payment_plans`) for one page of plans. Pass `limit` from 1 to 100 and pass `next_cursor` back as `cursor` until it is `null`.

Each plan has `plan_id`, `kind` (`one_time` or `recurring`), a terms `version`, and a `token` with `asset`, `amount`, `decimals` and `contract`. `period_seconds` and `grace_seconds` describe a subscription; both are `0` for a one-time plan.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client, type Models } from "@arx/nexo";

const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const plans: Models.PaymentPlan[] = [];
  let cursor: string | undefined;
  do {
    const page = await bot.getPaymentPlans({ limit: 100, ...(cursor ? { cursor } : {}) });
    plans.push(...page.items);
    cursor = page.next_cursor ?? undefined;
  } while (cursor);
  for (const plan of plans) {
    console.log(plan.plan_id, plan.kind, plan.token.asset, plan.token.amount);
  }
} finally {
  await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
from arx_nexo import Client, Config, models

with Client(Config(base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    cursor: str | None = None
    while True:
        query = models.PaymentPlansListQuery(limit=100, **({"cursor": cursor} if cursor else {}))
        page = bot.get_payment_plans(query)
        for item in page.items:
            plan = item.root
            print(plan.plan_id, plan.kind, plan.token.asset, plan.token.amount)
        cursor = page.next_cursor
        if cursor is None:
            break
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let config = Config::new(std::env::var("ARX_BOT_TOKEN")?, "https://signal.arx.pro/bot-api/v1", false)?;
    let bot = Client::new(config)?;
    let mut query = models::PaymentPlansListQuery { limit: Some(100), ..Default::default() };
    loop {
        let page = bot.get_payment_plans(&query).await?;
        for plan in &page.items {
            println!("{plan:?}");
        }
        match page.next_cursor {
            Some(cursor) => query.cursor = Some(cursor),
            None => break,
        }
    }
    Ok(())
}
```

{% endtab %}
{% endtabs %}

Reading plans, tokens or readiness never creates a purchase or a wallet request.

## Next steps <a href="#next-steps" id="next-steps"></a>

- [Send an invoice](invoices.md) for a plan.
- See which [networks and assets](networks.md) buyers can pay with.
