---
hidden: true
---

# Home1

## $ARX & XMTP

$ARX integrated into [XMTP](https://docs.xmtp.org/) provides flexible & scalable API to implement chat & money functionality in one app that allows easily do everything you want at one place.

ARX NET Slice (ARX) is a governance and utility token with a sophisticated on-chain sales system, featuring a "zap router" for easy, one-click conversion of other cryptocurrencies into $ARX.

XMTP is a secure web3 messaging protocol that allows developers to build applications with private, end-to-end encrypted chat where users own their data and conversations.

***

## Why building with $ARX?

ARX NET Slice ("ARX") is a 6‑decimal ERC‑20 token designed for governance and utility inside the ARX ecosystem. The project ships a complete on-chain sale architecture with upgradeable contracts, a zap router for 1‑click conversion (any token/ETH → USDC → ARX), and a modern web app to onboard users with clear quoting and slippage controls.

* Token: `ARX` (name: "ARX NET Slice", symbol: `ARX`, 6 decimals, Permit, Burnable, ERC20Votes)
* Sale: `ArxTokenSale` accepts USDC and mints ARX at an owner‑set price; forwards 100% of USDC to a treasury (silo)
* Zaps: `ArxZapRouter` takes ERC‑20/ETH, swaps to USDC via Uniswap V3, then calls `sale.buyFor()` to deliver ARX in a single transaction
* Frontend: Next.js app with wallet connect, token selector (USDC/ETH), Uniswap Quoter pricing, slippage controls, and live transaction preview

***

## Why building with XMTP?

Build with [XMTP](https://docs.xmtp.org/) to:

*   **Deliver secure and private messaging**

    Using the [Messaging Layer Security](https://docs.xmtp.org/protocol/security) (MLS) standard, a ratified [IETF](https://www.ietf.org/about/introduction/) standard, [XMTP](https://docs.xmtp.org/) provides end-to-end encrypted messaging with forward secrecy and post-compromise security.
*   **Provide spam-free chats**

    In any open and permissionless messaging ecosystem, spam is an inevitable reality, and [XMTP](https://docs.xmtp.org/) is no exception. However, with [XMTP](https://docs.xmtp.org/) [user consent preferences](https://docs.xmtp.org/chat-apps/user-consent/user-consent), developers can give their users spam-free chats displaying conversations with chosen contacts only.
*   **Build on native crypto rails**

    Build with [XMTP](https://docs.xmtp.org/) to tap into the capabilities of crypto and web3. Support decentralized identities, crypto transactions, and more, directly in a messaging experience.
*   **Empower users to own and control their communications**

    With apps built with [XMTP](https://docs.xmtp.org/), users own their conversations, data, and identity. Combined with the interoperability that comes with protocols, this means users can access their end-to-end encrypted communications using any app built with [XMTP](https://docs.xmtp.org/).
*   **Create with confidence**

    Developers are free to create the messaging experiences their users want-on a censorship-resistant protocol architected to last forever. Because [XMTP](https://docs.xmtp.org/) isn't a closed proprietary platform, developers can build confidently, knowing their access and functionality can't be revoked by a central authority.
