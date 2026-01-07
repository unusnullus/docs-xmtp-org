# 5 System Architecture

The ARX system architecture defines how privacy, scalability, and decentralization operate together within one framework. It connects communication, payments, and governance under a single, modular structure that ensures speed, reliability, and user sovereignty.

### The Technical Layers

Each component works independently but contributes to the overall network integrity and security across several distinct layers:

* **Identity Layer:** Establishes wallet-based user authentication and self-sovereign digital identity (SSI).
* **Messaging Layer:** Enables encrypted, off-chain communication without the need for centralized servers.
* **Network Layer:** Maintains reliability through validator and relay coordination using **Proof-of-Stake (PoS)** and **Proof-of-Relay** mechanisms.
* **Governance Layer:** Oversees decision-making, treasury control, and protocol upgrades through on-chain voting.
* **Security Framework:** Ensures protection at the device, protocol, and economic levels.
* **Scalability & Performance:** Optimizes throughput and efficiency for large-scale adoption.

---

### Structural Overview

This multi-layered approach ensures that the network remains fast enough for daily use while maintaining the cryptographic guarantees required for absolute privacy.

```mermaid
graph TD
    %% Global Styling
    accTitle: ARX System Layers
    accDescr: Visualizing the modular hierarchy of the ARX technical architecture.

    subgraph TOP ["Interaction & Governance"]
        L5[Governance Layer] --- L4[Security Framework]
    end

    subgraph MID ["Core Functionality"]
        L3[Identity Layer] --- L2[Messaging Layer]
    end

    subgraph BOT ["Infrastructure & Economics"]
        L1[Network Layer] --- L0[Scalability & Performance]
    end

    %% Flow of data/logic
    TOP --> MID
    MID --> BOT

    %% Apply Brand Palette
    style L5 fill:#7858ff,stroke:#fefeff,color:#fefeff
    style L4 fill:#7858ff,stroke:#fefeff,color:#fefeff
    
    style L3 fill:#346ddb,stroke:#fefeff,color:#fefeff
    style L2 fill:#346ddb,stroke:#fefeff,color:#fefeff
    
    style L1 fill:#2c2c2c,stroke:#7858ff,color:#fefeff
    style L0 fill:#2c2c2c,stroke:#7858ff,color:#fefeff

    style TOP fill:#2B3648,stroke:#346ddb,color:#fefeff
    style MID fill:#2B3648,stroke:#346ddb,color:#fefeff
    style BOT fill:#2B3648,stroke:#346ddb,color:#fefeff
```

{% hint style="info" %}
**Modular Resilience**
Because each layer is modular, the failure or compromise of a single non-core component does not jeopardize the integrity of the user's identity or the security of their assets.
{% endhint %}