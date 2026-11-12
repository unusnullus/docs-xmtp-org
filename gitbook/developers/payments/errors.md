---
description: "Error codes returned by payment operations and how to handle each one."
---

# Payment errors

Payment operations use the standard [error envelope](../errors.md). This page lists the codes you see most often with payments and what causes them.

| Code | Common causes | What to do |
| --- | --- | --- |
| `invalid_request` | A field breaks its limits, such as a `title` over 32 characters or a `payload` over 128 bytes. The request includes a field ARX sets, such as `chain` or `recipient`. The idempotency key is missing or invalid. The asset is not one you accept. The amount is outside the asset's limits. The `plan_id` does not exist, or `asset` and `amount` differ from the plan. A subscription plan was sent with `sendInvoice`. A rejection has no `error_message`, or an approval has one. A report date range is not 1 to 366 days. | Fix the request. Read plans and assets again from `getPaymentPlans` and `getPaymentTokens`. Use an invoice link for subscriptions. |
| `idempotency_key_reused` | You reused a key with a different body. | Resend the original body, or use a new key for a new order. |
| `query_expired` | You answered a `pre_checkout_query` after its 10-second deadline, answered it twice, or sent a query ID that does not exist for your bot. | Do nothing. The buyer can try again, which creates a new query. Answer faster. |
| `charge_not_found` | The charge does not exist or belongs to another bot. | Check the `charge_id` you saved from the payment updates. |
| `subscription_not_found` | The subscription does not exist or belongs to another bot. | Check the `subscription_id` you saved from the payment updates. |
| `payment_not_found` | The payment record does not exist or belongs to another bot. | Check the IDs you saved from the payment updates. |
| `merchant_not_ready` | Payments are not set up for your bot yet, for example the payout wallet is missing. | Finish setup in NexoCreator and check `getPaymentReadiness`. Do not retry until it reports ready. |
| `refund_amount_exceeded` | The refund amount is more than the charge or its remaining refundable amount. | Refund at most the remaining amount. |
| `access_restricted` | Your bot's payment setup is not available, or the buyer no longer has a chat with your bot when you request a refund. | Check the setup in NexoCreator. For refunds, ask the buyer to open the chat with your bot again. |
| `capability_not_available` | The operation or option is not supported, such as `is_canceled: false` when updating a subscription. | Use a supported option. |
| `user_not_found` | The `user_id` is unknown or not available to your bot, or you sent a chat ID instead of the user ID. | Use the buyer's decimal user ID from the payment update. |
| `upstream_unavailable` | A payment dependency is temporarily unavailable. It is also returned when you refund a charge that is not confirmed, when `getPaymentReadiness` runs before any accepted asset is set up in NexoCreator, and for every `getPaymentAnalytics` call until analytics are available. | Check `getPaymentReadiness` and the charge first. Then retry with the same idempotency key, honoring `Retry-After`. |
| `timeout` | The operation did not finish in time. | Retry with the same idempotency key. |

A successful refund request returns HTTP 202, not 200. It means the request was accepted, not that money moved. See [Refunds](refunds.md).

## Retry safely <a href="#retry-safely" id="retry-safely"></a>

Retry only with the original idempotency key and the original body. A retry then returns the original invoice or refund receipt instead of creating a second one. Never retry an uncertain request as a new purchase or a new refund. See [Idempotency](../making-requests.md#idempotency) and [Retries and SDK behavior](../errors.md#retries-and-sdk-behavior).

## Results that are not errors <a href="#results-that-are-not-errors" id="results-that-are-not-errors"></a>

- `getPaymentReadiness` returning `ready: false`.
- An empty `items` list from `getSubscriptions` or `getPaymentPlans`.
- `getEntitlement` returning `has_access: false`.

Handle these as normal results and keep them separate from request errors.
