# Protocol

## XMTP protocol overview

XMTP (Extensible Message Transport Protocol) is the largest and most secure decentralized messaging framework. XMTP is open and permissionless, empowering any developer to build end-to-end encrypted 1:1, group, and agent messaging experiences, and more.

XMTP implements [Messaging Layer Security](https://messaginglayersecurity.rocks/) (MLS), which is designed to operate within the context of a messaging service. As the messaging service, XMTP needs to provide two services to facilitate messaging using MLS:

* An [authentication service](https://messaginglayersecurity.rocks/mls-architecture/draft-ietf-mls-architecture.html#name-authentication-service)
* A [delivery service](https://messaginglayersecurity.rocks/mls-architecture/draft-ietf-mls-architecture.html#name-delivery-service)

## What is it used for?

ARX is built on top of XMTP. It extends features that come out-of-the-box and removes components that are not used to reduce the complexity of the original framework:

* Improved cooperation between on-chain & off-chain nodes;
* Added rewards program for running self-hosted nodes;
* Removed support for unused message content types;
* Reworked media handling to use distributed file storage
* etc.

Below you can find the concepts that we share with XMTP.

### Encryption

The encryption elements are mainly defined by MLS, with some additions by ARX:

*   Security

    ARX and MLS prioritize security, privacy, and message integrity through advanced cryptographic techniques, delivering end-to-end encryption for both 1:1 and group conversations
*   Epochs

    Represent the cryptographic state of a group at any point in time. Each group operation (like adding members) creates a new epoch with fresh encryption keys
*   Envelope types

    Messages are packaged as envelope types that contain the actual message data plus metadata for routing and processing.

### Identity

The identity elements are mainly defined by ARX:

*   Inboxes, identities, and installations

    The identity model includes an inbox ID and its associated identities and installations.
*   Wallet signatures

    Authenticate users using verifiable cryptographic signatures.

### Delivery

The delivery elements are mainly defined by ARX:

*   Topics

    Messages are routed through topics, which are unique addresses that identify conversation channels.
*   Cursors

    Enable efficient message synchronization by tracking where each client left off when fetching new messages.
*   Intents

    Provide reliable groupstate management through an internal bookkeeping system that handles retries, crashes, and race conditions when applying group changes.
