# 5 System Architecture

The ARX system architecture defines how privacy and scalability operate together within one framework. It connects communication, payments, and governance under a single, modular structure.

This section describes the system as it runs today. Where a layer also has a planned model, the page says so.

### The Technical Layers

Each component works independently but contributes to the overall network integrity and security across several distinct layers:

* **Identity Layer:** Establishes wallet-based user authentication.
* **Messaging Layer:** Delivers end-to-end encrypted, off-chain communication between users.
* **Network Layer:** The infrastructure that carries messages, VPN traffic, and files. It is operated by ARX today; a model with independent operators is planned.
* **Governance Layer:** The on-chain voting contracts and the planned governance process.
* **Security Framework:** Protection at the device and protocol levels.
* **Scalability & Performance:** Optimizes throughput and efficiency for large-scale adoption.

---

### Structural Overview

This multi-layered approach keeps the system fast enough for daily use while keeping message content end-to-end encrypted.

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

    subgraph BOT ["Infrastructure"]
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
Identity and wallet keys are held on the user's device, so a failure in another layer does not expose them.
{% endhint %}