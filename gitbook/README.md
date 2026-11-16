# Introduction

ARX is a daily stack application that replaces your app pile with one private platform.\
Chat, pay, connect (VPN + eSIM), shop, store files, and automate actions in a single place.

Install ARX once and you’re ready.\
No switching between apps. No extra services to set up. No scattered logins and settings.

Everything is bound to your wallet address (EOA).\
You don’t need to hand over extra personal data to get started.

{% hint style="info" %}
Some regulated features (for example, certain financial services) may require verification.\
Core messaging and network features are designed to work without traditional accounts.
{% endhint %}

ARX is built for global scale, strong security, and regulatory readiness.

### What you can do with ARX

* **Message privately**: end-to-end encrypted chat and file sharing between users.
* **Pay and spend**: transact in-chat and use the ARX card for daily spending.
* **Run commerce**: bots can sell one-time purchases and subscriptions, paid from the ARX wallet.
* **Connect securely**: a built-in VPN and eSIM data plans.
* **Store encrypted data**: ARX Cloud, an encrypted file vault in the app.
* **Automate actions**: Nexo bots and Mini Apps run inside chats.

### How ARX works (high level)

ARX separates private user activity from public coordination.\
Message content stays off-chain and encrypted.\
The ARX token and its governance and staking logic are on-chain smart contracts.

The system is organized into four layers:

1. Identity
2. Messaging
3. Network
4. Governance

This modular design keeps each layer independent.\
It also keeps the overall system cohesive and upgradeable.

### What runs today

* Messages between users are end-to-end encrypted with the Signal Protocol. Keys are created and kept on the device.
* Encrypted messages are relayed by backend services that ARX operates. These services cannot read message content.
* The wallet is self-custodial. Private keys stay on the device.
* The ARX token, sale, governance, staking, and service-registry contracts are deployed on the Sepolia testnet. See [Architecture](usdarx/architecture.md).

### Planned network model

The sections on tokenomics, the economic model, and governance describe where ARX is going.\
They are plans, not a description of a live network.

{% hint style="info" %}
**Terms used for the planned model**:

* **Proof-of-Stake (PoS)**: validators secure the chain by staking.
* **Proof-of-Relay (PoR)**: performance rewards based on verified service metrics.
* **DAO**: token-governed decision making for upgrades and treasury.
* **DePIN**: decentralized physical infrastructure network. Nodes earn for real services.
{% endhint %}

The plan is a hybrid **PoS + PoR** model in which independent operators run relay and service nodes and earn rewards for measurable contribution, such as uptime, throughput, and latency.

The ARX token is designed as the network’s utility and governance asset, for staking, node rewards, DAO voting, and payments for ARX services.

Governance is designed to run through the ARX DAO and to move through phased decentralization, starting with foundation support and moving toward community control.

### Where to go next

* Why ARX exists: [1 Background & Motivation](home/1-background-and-motivation/)
* What it fixes: [2 Problem Definition](home/2-problem-definition/)
* Core principles: [3 The ARX Solution](home/3-the-arx-solution/)
* On-chain vs off-chain split: [3.2 On-Chain trust and off-chain privacy](home/3-the-arx-solution/3.2-on-chain-trust-and-off-chain-privacy.md)
* Modules: [4 Product Overview](home/4-product-overview/)
* Layer breakdown: [5 System architecture](home/5-system-architecture/)
* Wallet-based identity (EOA): [5.1 Identity layer](home/5-system-architecture/5.1-identity-layer.md)
* The network today and the planned model: [5.3 Network layer](home/5-system-architecture/5.3-network-layer.md)
* Planned incentives: [8.3 Proof-of-Relay Rewards Distribution](home/8-economic-model/8.3-proof-of-relay-rewards-distribution.md)
* Planned governance: [9 Governance model](home/9-governance-model/)
* Build a bot: [Nexo bot developer docs](developers/README.md)
