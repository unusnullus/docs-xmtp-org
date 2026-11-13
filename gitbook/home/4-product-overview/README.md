# 4 Product Overview

ARX is built around three functional verticals that together form a unified privacy and utility ecosystem. This architecture turns ARX into a complete digital environment where users can communicate, transact, and connect securely without leaving the platform.

### The Three Verticals

Each vertical operates independently but becomes exponentially more powerful when combined under one wallet-based identity:

1.  **Communication:** A secure, end-to-end encrypted messenger designed for private interaction and high-stakes coordination.
2.  **Fintech:** Integrated wallet, card, and payment tools that enable frictionless digital commerce, allowing users to manage assets and make payments globally.
3.  **Connection:** A built-in VPN, eSIM data plans, and encrypted cloud storage.

---

### The Unified Experience

By integrating these services, ARX eliminates the "fragmentation trap," allowing for a seamless transition between social, financial, and network activities.

```mermaid
graph TD
    %% Global Styling
    accTitle: ARX Product Verticals
    accDescr: Visualizing the three core pillars of the ARX product suite connected by a unified identity.

    ID{Unified Wallet Identity}

    ID --- V1[Communication]
    ID --- V2[Fintech]
    ID --- V3[Connection]

    subgraph Messenger ["ARX Messenger"]
        V1 --- M1[E2EE Chat]
        V1 --- M2[Groups & Channels]
    end

    subgraph Finance ["ARX Fintech"]
        V2 --- F1[P2P Payments]
        V2 --- F2[Digital Cards]
    end

    subgraph Network ["ARX Connection"]
        V3 --- N1[Built-in VPN]
        V3 --- N2[Global eSIM]
    end

    %% Apply Brand Palette
    style ID fill:#7858ff,stroke:#fefeff,color:#fefeff,stroke-width:2px
    
    style V1 fill:#346ddb,stroke:#fefeff,color:#fefeff
    style V2 fill:#346ddb,stroke:#fefeff,color:#fefeff
    style V3 fill:#346ddb,stroke:#fefeff,color:#fefeff

    style Messenger fill:#2B3648,stroke:#346ddb,color:#fefeff
    style Finance fill:#2B3648,stroke:#346ddb,color:#fefeff
    style Network fill:#2B3648,stroke:#346ddb,color:#fefeff

    style M1 fill:#2c2c2c,stroke:#7858ff,color:#fefeff
    style M2 fill:#2c2c2c,stroke:#7858ff,color:#fefeff
    style F1 fill:#2c2c2c,stroke:#7858ff,color:#fefeff
    style F2 fill:#2c2c2c,stroke:#7858ff,color:#fefeff
    style N1 fill:#2c2c2c,stroke:#7858ff,color:#fefeff
    style N2 fill:#2c2c2c,stroke:#7858ff,color:#fefeff
```

{% hint style="info" %}
**Identity as the Anchor**
While these verticals provide diverse functionalities, they are all anchored by a single cryptographic identity.
{% endhint %}