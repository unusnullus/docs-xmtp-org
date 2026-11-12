---
description: "Sell manually renewed subscriptions, follow their state and check a subscriber's access."
---

# Subscriptions and renewals

A subscription gives a buyer access for one billing period at a time. The buyer pays each period with a new transfer that they approve in the app wallet. ARX never charges a wallet automatically, and the buyer never grants a standing spending permission.

## Start a subscription <a href="#start-a-subscription" id="start-a-subscription"></a>

1. Create a subscription plan in NexoCreator. See [Set up payments](setup.md).
2. Create an [invoice link](invoices.md#create-an-invoice-link) with the plan's `plan_id`, `asset` and `amount`.
3. Share the link. When a buyer pays and the transfer is confirmed, your bot receives `successful_payment`. The current `successful_payment` service does not include `subscription_id`. Read `subscription_updated` or `getSubscriptions` and match the buyer and plan to obtain it.

Each buyer gets one subscription per link. If the buyer opens the same link again, they return to that subscription.

## Lifecycle <a href="#lifecycle" id="lifecycle"></a>

| State | Access | Meaning |
| --- | --- | --- |
| `active` | Yes | The current period is paid. |
| `past_due` | Yes | The period ended and the grace period is running. The buyer can still renew. |
| `canceled` | Until the period ends | The buyer or your bot canceled. No reminders are sent. |
| `expired` | No | The period and grace ended without a confirmed renewal. |
| `refunded` | No | The paid periods were refunded. |

`ended` is also accepted as a filter value, but subscriptions do not currently reach that state.

### Renewal reminders <a href="#renewal-reminders" id="renewal-reminders"></a>

24 hours before the period ends, ARX sends the buyer a renewal reminder in your bot's chat and as a push notification. On a daily plan, the reminder arrives as soon as the period starts. The buyer renews from the reminder or from their subscriptions list in Messenger.

Your bot can receive the same moment as a `renewal_due` update. It is opt-in: add `renewal_due` to your filter, for example `allowed_updates=default,renewal_due`. The update contains `user`, `subscription_id`, `plan_id`, `current_period_end_at` and `grace_ends_at`. Use it to send your own message. It does not mean anything was paid.

The reminders the buyer sees are `subscription_notice` service messages in the buyer's chat. Your bot does not receive them; use `renewal_due` and `subscription_updated`.

### Renewing <a href="#renewing" id="renewing"></a>

A renewal runs the same checkout as the first payment: your bot receives a `pre_checkout_query`, then `successful_payment` after the transfer is confirmed. Renewals charge the plan's current price, so a price change applies from each subscriber's next renewal. The buyer sees the price before approving.

When a renewal is confirmed:

- **Before access ends**, including during grace, the new period starts when the previous period ended. Renewing early adds one full period.
- **After the subscription expired**, the new period starts when the renewal is confirmed.

A renewal also clears an earlier cancellation.

### Grace and expiry <a href="#grace-and-expiry" id="grace-and-expiry"></a>

When the period ends without a renewal, the subscription becomes `past_due` and access continues until `grace_ends_at`. The grace period is 0 to 14 days, as set on the plan. If no renewal is confirmed by then, the subscription becomes `expired` and access ends. A later confirmed renewal restores access.

Your bot receives a `subscription_updated` update when the state changes. It is in the default update filter. It contains `user`, `subscription_id`, `state`, `current_period_end_at`, `access_until` and, for cancellations, `canceled_by` (`user`, `bot` or `admin`). It does not include `invoice_payload`; identify the subscription by `subscription_id`.

## Check access <a href="#check-access" id="check-access"></a>

Call `getEntitlement` (`get_entitlement`) with the buyer's user ID before you serve paid features. ARX computes the answer from confirmed payments, grace periods, refunds and reversals.

| Field | Meaning |
| --- | --- |
| `has_access` | Whether the user can use your paid features now. |
| `source` | `subscription`, `one_time` or `none`. |
| `access_until` | When access ends, or `null`. A one-time purchase has `null`. |

Always read `has_access`. Do not compute access from `access_until`, a checkout answer or a pending transaction.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const userId = process.env.USER_ID!;
const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const access = await bot.getEntitlement(userId);
  if (access.has_access) {
    console.log(`Access from ${access.source} until ${access.access_until ?? "no end date"}`);
  } else {
    console.log("No access; send an invoice link");
  }
} finally {
  await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

user_id = os.environ["USER_ID"]

with Client(Config(base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    access = bot.get_entitlement(user_id)
    if access.has_access:
        print(f"Access from {access.source} until {access.access_until or 'no end date'}")
    else:
        print("No access; send an invoice link")
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let user_id = std::env::var("USER_ID")?;
    let config = Config::new(std::env::var("ARX_BOT_TOKEN")?, "https://signal.arx.pro/bot-api/v1", false)?;
    let bot = Client::new(config)?;
    let access = bot.get_entitlement(&user_id).await?;
    if access.has_access {
        println!("Access from {:?} until {:?}", access.source, access.access_until);
    } else {
        println!("No access; send an invoice link");
    }
    Ok(())
}
```

{% endtab %}
{% endtabs %}

## List subscribers <a href="#list-subscribers" id="list-subscribers"></a>

`getSubscriptions` (`get_subscriptions`) returns one page of your bot's subscriptions. Filter by `state` or `plan_id`. Pass `limit` from 1 to 100 (default 20) and pass `next_cursor` back as `cursor` until it is `null`.

Each subscription has:

| Field | Meaning |
| --- | --- |
| `subscription_id`, `user`, `plan_id`, `state` | Identity and current state. |
| `started_at` | Start of the first period whose payment is still confirmed. |
| `current_period_end_at` | End of the current paid period. |
| `access_until` | When access ends, including grace. `null` when there is no access. |
| `grace_ends_at`, `renewal_due_at` | Present when they apply. |
| `periods_paid` | Number of paid periods. |
| `has_access` | Whether the subscriber has access now. |
| `total_paid` | Sum of confirmed payments received by your wallet, as one entry for the plan's asset. Refunds are not subtracted; reversed payments are excluded. |
| `last_charge` | `charge_id`, `at` and `tx_hash` of the latest payment. |
| `canceled_by` | `user`, `bot` or `admin`, when canceled. |

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const bot = new Client({ baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  let cursor: string | undefined;
  do {
    const page = await bot.getSubscriptions({ state: "past_due", limit: 100, ...(cursor ? { cursor } : {}) });
    for (const sub of page.items) {
      console.log(sub.user.id, sub.plan_id, sub.grace_ends_at);
    }
    cursor = page.next_cursor ?? undefined;
  } while (cursor);
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
        filters = {"cursor": cursor} if cursor else {}
        page = bot.get_subscriptions(models.SubscriptionsListQuery(state="past_due", limit=100, **filters))
        for sub in page.items:
            print(sub.user.id, sub.plan_id, sub.grace_ends_at)
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
    let mut query = models::SubscriptionsListQuery {
        state: Some(models::SubscriptionsListQueryState::PastDue),
        limit: Some(100),
        ..Default::default()
    };
    loop {
        let page = bot.get_subscriptions(&query).await?;
        for sub in &page.items {
            println!("{} {} {:?}", sub.user.id, sub.plan_id, sub.grace_ends_at);
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

## Cancel a subscription <a href="#cancel-a-subscription" id="cancel-a-subscription"></a>

The buyer can cancel from their subscriptions list in Messenger. Your bot can cancel with `PATCH /subscriptions/{subscription_id}`. Send the subscriber's `user_id` and `is_canceled: true`, with an idempotency key:

```sh
curl --fail-with-body --max-time 30 -X PATCH \
  "$ARX_BOT_API_BASE_URL/subscriptions/${SUBSCRIPTION_ID}" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H 'Content-Type: application/json' \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  --data "{\"user_id\":\"${USER_ID}\",\"is_canceled\":true}"
```

Cancellation stops reminders and keeps access until the current period ends. The response is the updated subscription. Canceling twice has no further effect. `is_canceled: false` is not supported and returns `capability_not_available`; the buyer resumes by renewing.

In the SDKs, call the generated method:

- TypeScript: `bot.subscriptionsUpdate(subscriptionId, { key, body: { user_id: userId, is_canceled: true } })`
- Python: `bot.subscriptions_update(subscription_id, body=models.UpdateSubscription(user_id=user_id, is_canceled=True), key=key)`
- Rust: `bot.subscriptions_update(&subscription_id, &[], Some(&models::UpdateSubscription::new(true, user_id)), Some(&key)).await?`

## Related <a href="#related" id="related"></a>

- [Refunds](refunds.md) explains how a refund affects access.
- [Balances and reports](reports.md) shows your receiving wallet balance.
