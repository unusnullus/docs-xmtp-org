# Get started

## Get started building with XMTP

***

[XMTP](https://docs.xmtp.org/) (Extensible Message Transport Protocol) is the largest and most secure decentralized messaging network. [XMTP](https://docs.xmtp.org/) is open and permissionless, empowering any developer to build end-to-end encrypted 1:1, group, and agent messaging experiences, and more.

***

### 🛠️ Phase 0: Explore XMTP developer tools

*   Pick your SDK:

    [Browser](sdks.md#get-started-with-the-xmtp-browser-sdk) [Node](sdks.md#get-started-with-the-xmtp-node-sdk) [React Native](sdks.md#get-started-with-the-xmtp-react-native-sdk) [Android](sdks.md#get-started-with-the-xmtp-android-sdk) [iOS](sdks.md#get-started-with-the-xmtp-ios-sdk)
* [Run a local XMTP node](https://github.com/xmtp/xmtp-local-node/tree/main) for development and testing.

***

### 💬 Phase I: Build core messaging

1. [Create an EOA or SCW signer](https://docs.xmtp.org/chat-apps/core-messaging/create-a-signer).
2. [Create an XMTP client](https://docs.xmtp.org/chat-apps/core-messaging/create-a-client). Be sure to set the `appVersion` client option.
3. [Check if an identity is reachable on XMTP](https://docs.xmtp.org/chat-apps/core-messaging/create-conversations#check-if-an-identity-is-reachable).
4.  Create a [group chat](https://docs.xmtp.org/chat-apps/core-messaging/create-conversations) or [direct message](https://docs.xmtp.org/chat-apps/core-messaging/create-conversations) (DM) conversation.

    With [XMTP](https://docs.xmtp.org/), "conversation" refers to both group chat and DM conversations.
5. [Send messages](https://docs.xmtp.org/chat-apps/core-messaging/send-messages) in a conversation.
6. Manage group chat [permissions](https://docs.xmtp.org/chat-apps/core-messaging/group-permissions) and [metadata](https://docs.xmtp.org/chat-apps/core-messaging/group-metadata).
7. [Manage identities, inboxes, and installations](https://docs.xmtp.org/chat-apps/core-messaging/manage-inboxes).
8. Be sure to [observe rate limits](https://docs.xmtp.org/chat-apps/core-messaging/rate-limits).

***

### 📩 Phase II: Manage conversations and messages

1. [List existing conversations](https://docs.xmtp.org/chat-apps/list-stream-sync/list) from local storage.
2. [Stream new conversations](https://docs.xmtp.org/chat-apps/list-stream-sync/stream) from the network.
3. [Stream new messages](https://docs.xmtp.org/chat-apps/list-stream-sync/stream) from the network.
4. [Sync new conversations](https://docs.xmtp.org/chat-apps/list-stream-sync/sync-and-syncall) from the network.
5. [Sync a specific conversation's messages and preference updates](https://docs.xmtp.org/chat-apps/list-stream-sync/sync-and-syncall) from the network.

***

### 💅🏽 Phase III: Enhance the user experience

1. [Implement user consent](https://docs.xmtp.org/chat-apps/user-consent/support-user-consent), which provides a consent value of either **unknown**, **allowed** or **denied** to each of a user's contacts. You can use these consent values to filter conversations. For example:
   * Conversations with **allowed** contacts go to a user's main inbox
   * Conversations with **unknown** contacts go to a possible spam tab
   * Conversations with **denied** contacts are hidden from view.
2. Support rich [content types](https://docs.xmtp.org/chat-apps/content-types/content-types).
   * [Onchain transactions](https://docs.xmtp.org/chat-apps/content-types/transactions)
   * [Onchain transaction references](https://docs.xmtp.org/chat-apps/content-types/transaction-refs)
3. [Implement push notifications](https://docs.xmtp.org/chat-apps/push-notifs/understand-push-notifs), if applicable.

***

### 🧪 Phase IV: Test and debug

* [Stress and burn-in test](https://docs.xmtp.org/chat-apps/debug-your-app#xmtp-debug) your chat app.
* [Enable file logging](https://docs.xmtp.org/chat-apps/debug-your-app#file-logging).
* [Capture network statistics](https://docs.xmtp.org/chat-apps/debug-your-app#network-statistics).
* Found a bug or need help? Contact [dev support](https://docs.xmtp.org/chat-apps/intro/dev-support).
