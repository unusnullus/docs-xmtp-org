# 7 Tokenomics

The ARX token is the core economic engine of the ecosystem. It defines how value is generated, distributed, and sustained among users, validators, developers, and governance participants. Designed for long-term equilibrium, the token supports every layer of network coordination including payments, staking, rewards, and decision-making.

### Framework Overview

The ARX economy operates on a **"Stake & Earn"** and **"Use & Contribute"** model. This section details the framework that maintains network health:

* **Token Specifications:** Core parameters including total supply, emission rates, and integration with the ARX Proof-of-Stake (PoS) chain.
* **Distribution and Emission:** A fair allocation and inflation design that promotes predictability and gradual supply stabilization.
* **Utility & Governance:** Practical applications for payments, staking, and participating in on-chain decision-making.
* **Economic Flow & Sustainability:** Revenue sources, consumption mechanisms, and DAO-managed funding.

---

### A Circular Incentive Structure

ARX’s tokenomics eliminate reliance on advertising or custodial fees. Instead, it creates a circular, incentive-driven structure where transaction activity and service usage generate ongoing value. Over time, declining emissions transition the system toward self-sufficiency, supported by subscription revenues and DAO-managed treasury operations.

```mermaid
graph TD
    %% Global Styling
    accTitle: ARX Tokenomics Overview
    accDescr: High-level view of the four pillars of the ARX economic engine.

    TOKEN((ARX Token))
    
    subgraph Pillars ["Core Framework"]
        P1[Token Specs & PoS]
        P2[Distribution & Emission]
        P3[Utility & Governance]
        P4[Economic Flow]
    end

    subgraph Participants ["Economic Roles"]
        U[Users]
        V[Validators]
        D[Developers]
        G[DAO Governance]
    end

    TOKEN --- Pillars
    P3 --- U
    P1 --- V
    P4 --- D
    P3 --- G

    %% Apply Brand Palette
    style TOKEN fill:#7858ff,stroke:#fefeff,color:#fefeff,stroke-width:3px
    
    style P1 fill:#346ddb,stroke:#fefeff,color:#fefeff
    style P2 fill:#346ddb,stroke:#fefeff,color:#fefeff
    style P3 fill:#346ddb,stroke:#fefeff,color:#fefeff
    style P4 fill:#346ddb,stroke:#fefeff,color:#fefeff

    style Pillars fill:#2B3648,stroke:#346ddb,color:#fefeff
    style Participants fill:#2c2c2c,stroke:#7858ff,color:#fefeff
    
    style U fill:#2c2c2c,stroke:#7858ff,color:#fefeff
    style V fill:#2c2c2c,stroke:#7858ff,color:#fefeff
    style D fill:#2c2c2c,stroke:#7858ff,color:#fefeff
    style G fill:#2c2c2c,stroke:#7858ff,color:#fefeff
```

{% hint style="info" %}
**The Sustainable Shift**
By linking token value to the actual utility and reliability of the network, ARX establishes a compliant digital economy where participants are directly rewarded for their contribution to private communication and secure connectivity.
{% endhint %}