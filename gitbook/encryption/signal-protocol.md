# Signal protocol

ARX Messenger encrypts conversations between users end to end with the Signal Protocol. Keys are generated and stored on the device, and each device has its own sessions. The specifications below describe the parts ARX uses.

### [X3DH](https://signal.org/docs/specifications/x3dh/)

This document describes the "X3DH" (or "Extended Triple Diffie-Hellman") key agreement protocol. X3DH establishes a shared secret key between two parties who mutually authenticate each other based on public keys. X3DH provides forward secrecy and cryptographic deniability.

### [Double Ratchet](https://signal.org/docs/specifications/doubleratchet/)

This document describes the Double Ratchet algorithm, which is used by two parties to exchange encrypted messages based on a shared secret key. The parties derive new keys for every Double Ratchet message so that earlier keys cannot be calculated from later ones. The parties also send Diffie-Hellman public values attached to their messages. The results of Diffie-Hellman calculations are mixed into the derived keys so that later keys cannot be calculated from earlier ones. These properties give some protection to earlier or later encrypted messages in case of a compromise of a party's keys.

### [XEdDSA](https://signal.org/docs/specifications/xeddsa/)

This document describes how to create and verify EdDSA-compatible signatures using public key and private key formats initially defined for the X25519 and X448 elliptic curve Diffie-Hellman functions. X3DH uses these signatures to sign prekeys.
