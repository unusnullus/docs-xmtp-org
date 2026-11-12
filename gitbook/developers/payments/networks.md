---
description: "The networks and assets buyers can pay with, their decimals, and who pays network fees."
---

# Networks, assets and fees

Payments use the same EVM networks and assets as the ARX app wallet. Each network supports its native coin, USDT and USDC.

## Supported networks and assets <a href="#supported-networks-and-assets" id="supported-networks-and-assets"></a>

| Network | Chain ID | Asset | Contract | Decimals |
| --- | --- | --- | --- | --- |
| Ethereum | `eip155:1` | ETH | native | 18 |
| Ethereum | `eip155:1` | USDC | `0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48` | 6 |
| Ethereum | `eip155:1` | USDT | `0xdac17f958d2ee523a2206206994597c13d831ec7` | 6 |
| BNB Chain | `eip155:56` | BNB | native | 18 |
| BNB Chain | `eip155:56` | USDT | `0x55d398326f99059ff775485246999027b3197955` | **18** |
| BNB Chain | `eip155:56` | USDC | `0x8ac76a51cc950d9822d68b83fe1ad97b32cd580d` | **18** |
| Polygon | `eip155:137` | POL | native | 18 |
| Polygon | `eip155:137` | USDT | `0xc2132d05d31c914a87c6611c10748aeb04b58e8f` | 6 |
| Polygon | `eip155:137` | USDC | `0x3c499c542cef5e3811e1192ce70d8cc03d5c3359` | 6 |
| Base | `eip155:8453` | ETH | native | 18 |
| Base | `eip155:8453` | USDC | `0x833589fcd6edb6e08f4c7c32d4f71b54bda02913` | 6 |
| Base | `eip155:8453` | USDT | `0xfde4c96c8593536e31f229ea8f37b2ada2699bb2` | 6 |

Asset IDs follow the pattern `<chain>/native` for a native coin and `<chain>/erc20:<contract>` for a token. For example, USDT on BNB Chain is `eip155:56/erc20:0x55d398326f99059ff775485246999027b3197955`.

USDT and USDC have 18 decimals on BNB Chain and 6 on the other networks. Always read `decimals` from the catalog or the invoice.

Read the live list with `getPaymentTokens` (`get_payment_tokens`) instead of copying this table into your code. The catalog is the source of truth for asset IDs, decimals and price limits. See [Read the accepted assets](setup.md#read-the-accepted-assets).

Non-EVM networks such as Bitcoin, Solana or Tron are not available for bot payments, even where the app wallet holds those coins. Receiving wallets must be EVM addresses.

## Price limits <a href="#price-limits" id="price-limits"></a>

Each asset has an inclusive `min_amount` and `max_amount` in base units. Invoice and plan prices outside these limits are rejected with `invalid_request`. Read the limits from the catalog.

## Fees <a href="#fees" id="fees"></a>

- **Network fee for a payment.** The buyer's wallet pays the network fee in the network's native coin, on top of the price. It is never taken from the invoice amount.
- **Platform commission.** ARX can charge a commission on each payment. It is 0 % today, and ARX can change it. The commission is deducted from the price: the buyer still pays exactly the invoice `amount`, sent as two transfers that the buyer approves in the same checkout, and your receiving wallet gets the price minus the commission. The `token.amount` in `successful_payment` is the amount your wallet received. Refunds return at most that amount; the commission is not refunded.
- **Network fee for a refund.** The bot owner's wallet pays it when sending the refund.

A buyer needs enough of the native coin on the same network to cover the network fee, including when paying in USDT or USDC.
