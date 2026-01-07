# Signal protocol

### [XEdDSA and VXEdDSA](https://signal.org/docs/specifications/xeddsa/)

This document describes how to create and verify EdDSA-compatible signatures using public key and private key formats initially defined for the X25519 and X448 elliptic curve Diffie-Hellman functions. This document also describes "VXEdDSA" which extends XEdDSA to make it a verifiable random function, or VRF.

### [Double Ratchet](https://signal.org/docs/specifications/doubleratchet/)

This document describes the Double Ratchet algorithm, which is used by two parties to exchange encrypted messages based on a shared secret key. The parties derive new keys for every Double Ratchet message so that earlier keys cannot be calculated from later ones. The parties also send Diffie-Hellman public values attached to their messages. The results of Diffie-Hellman calculations are mixed into the derived keys so that later keys cannot be calculated from earlier ones. These properties give some protection to earlier or later encrypted messages in case of a compromise of a party's keys.

### [X3DH](https://signal.org/docs/specifications/x3dh/)

This document describes the "X3DH" (or "Extended Triple Diffie-Hellman") key agreement protocol. X3DH establishes a shared secret key between two parties who mutually authenticate each other based on public keys. X3DH provides forward secrecy and cryptographic deniability.

### [PQXDH](https://signal.org/docs/specifications/pqxdh/)

This document describes the "PQXDH" (or "Post-Quantum Extended Diffie-Hellman") key agreement protocol. PQXDH establishes a shared secret key between two parties who mutually authenticate each other based on public keys. PQXDH provides post-quantum forward secrecy and a form of cryptographic deniability but still relies on the hardness of the discrete log problem for mutual authentication in this revision of the protocol.

### [ML-KEM Braid](https://signal.org/docs/specifications/mlkembraid/)

This document describes the ML-KEM Braid protocol. ML-KEM Braid is a sparse continuous key agreement protocol that uses ML-KEM to allow two parties to agree on a shared secret key in a way that provides post-quantum forward secrecy and post-compromise security.
