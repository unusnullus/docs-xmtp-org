---
description: "Read your receiving wallet balance. Daily payment reports are not available yet."
---

# Balances and reports

Your bot can read the current balance of its receiving wallet. The balance does not confirm a payment or grant access. Daily reports are described below but are not available yet.

## Read the receiving wallet balance <a href="#read-the-receiving-wallet-balance" id="read-the-receiving-wallet-balance"></a>

`getMerchantBalance` (`get_merchant_balance`) returns the on-chain balance of your receiving wallet for each asset you accept. It takes no parameters. Each entry in `balances` has `chain`, `asset`, an exact `amount` in base units and `decimals`. Amounts stay separate per network and asset. A zero balance is the string `"0"`.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const { balances } = await bot.getMerchantBalance();
  for (const balance of balances) {
    console.log(balance.chain, balance.asset, balance.amount, balance.decimals);
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
    for balance in bot.get_merchant_balance().balances:
        print(balance.chain, balance.asset, balance.amount, balance.decimals)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let config = Config::new(std::env::var("ARX_BOT_TOKEN")?, "https://signal.arx.pro/bot-api/v1", false)?;
    let bot = Client::new(config)?;
    for balance in bot.get_merchant_balance().await?.balances {
        println!("{} {} {} {}", balance.chain, balance.asset, balance.amount, balance.decimals);
    }
    Ok(())
}
```

{% endtab %}
{% endtabs %}

The balance is observed on chain within the last 30 seconds. It is the whole wallet, so it can include money that did not come from your bot, and it changes after any transfer. An empty `balances` list means nothing was observed; it is not a zero balance.

## Read daily reports <a href="#read-daily-reports" id="read-daily-reports"></a>

`getPaymentAnalytics` (`get_payment_analytics`) returns daily totals for a date range.

Daily payment analytics are not available yet. `getPaymentAnalytics` returns `upstream_unavailable` until an analytics source is configured; this is not an empty report.

| Parameter | Required | Rules |
| --- | --- | --- |
| `from` | Yes | First UTC date, `YYYY-MM-DD`, included. |
| `to` | Yes | Last UTC date, `YYYY-MM-DD`, excluded. 1 to 366 days after `from`. |
| `granularity` | No | `day`, the only value and the default. |
| `plan_id` | No | Report only one plan. |

The response repeats `from`, `to`, `granularity` and `plan_id`, and lists `buckets` in ascending date order. Each bucket has:

| Field | Type |
| --- | --- |
| `date` | UTC date. |
| `revenue` | One entry per asset, with `asset`, `amount` and `decimals`. |
| `revenue_usd_cents`, `mrr_usd_cents` | Integer strings, or `null` when unknown. |
| `avg_tenure_days` | Decimal string, or `null` when unknown. |
| `new`, `renewals`, `failed_renewals`, `past_due`, `churned`, `reactivated`, `active_at_end` | Subscription counts. |

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const report = await bot.getPaymentAnalytics({ from: "2026-11-01", to: "2026-11-08" });
  for (const day of report.buckets) {
    console.log(day.date, day.new, day.renewals, day.revenue);
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
    report = bot.get_payment_analytics(models.PaymentAnalyticsGetQuery.model_validate({"from": "2026-11-01", "to": "2026-11-08"}))
    for day in report.buckets:
        print(day.date, day.new, day.renewals, day.revenue)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config, models};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let config = Config::new(std::env::var("ARX_BOT_TOKEN")?, "https://signal.arx.pro/bot-api/v1", false)?;
    let bot = Client::new(config)?;
    let query = models::PaymentAnalyticsGetQuery::new("2026-11-01".into(), "2026-11-08".into());
    let report = bot.get_payment_analytics(&query).await?;
    for day in &report.buckets {
        println!("{} {} {} {:?}", day.date, day.new, day.renewals, day.revenue);
    }
    Ok(())
}
```

{% endtab %}
{% endtabs %}

Days without activity have no bucket; do not treat a missing day as zero. A `null` metric is unknown, not zero. Never add amounts of different assets together, and never convert them to floating point. Request the report again to see corrections after a payment is reversed.
