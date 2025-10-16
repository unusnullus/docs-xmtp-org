# SDKs

## Available SDKs

***

## Get started with the XMTP Browser SDK

Use the [XMTP Browser SDK](https://github.com/xmtp/xmtp-js/tree/main/sdks/browser-sdk) to build web-based apps, tools, and experiences with secure, private, and decentralized messaging.

The guide provides some quickstart code, as well as a map to building a [secure chat app](https://docs.xmtp.org/protocol/security) with XMTP, including support for:

* End-to-end encrypted direct message and group chat conversations
* Rich content types (attachments, reactions, replies, and more)
* Spam-free chats using user consent preferences

***

### Quickstart

{% tabs %}
{% tab title="Browser" %}
```javascript
// 1. Create an EOA or SCW signer.
// Details depend on your app's wallet implementation.
import type { Signer, Identifier } from '@xmtp/browser-sdk';
 
const signer: Signer = {
  type: 'EOA',
  getIdentifier: () => ({
    identifier: '0x...', // Ethereum address as the identifier
    identifierKind: 'Ethereum',
  }),
  signMessage: async (message: string): Uint8Array => {
    // typically, signing methods return a hex string
    // this string must be converted to bytes and returned in this function
  },
};
 
// 2. Create the XMTP client
import { Client } from '@xmtp/browser-sdk';
const client = await Client.create(signer, {
  // Note: dbEncryptionKey is not used for encryption in browser environments
});
 
// 3. Start conversations
const group = await client.conversations.newGroup(
  [bo.inboxId, caro.inboxId],
  createGroupOptions /* optional */
);
 
// 4. Send messages
await group.send('Hello everyone');
 
// 5. List, stream, and sync
// List existing conversations
const allConversations = await client.conversations.list({
  consentStates: [ConsentState.Allowed],
});
const allGroups = await client.conversations.listGroups({
  consentStates: [ConsentState.Allowed],
});
const allDms = await client.conversations.listDms({
  consentStates: [ConsentState.Allowed],
});
// Stream new messages
const stream = await client.conversations.streamAllMessages({
  consentStates: [ConsentState.Allowed],
  onValue: (message) => {
    console.log('New message:', message);
  },
  onError: (error) => {
    console.error(error);
  },
});
 
// Or use for-await loop
for await (const message of stream) {
  console.log('New message:', message);
}
// Sync all new welcomes, preference updates, conversations,
// and messages from allowed conversations
await client.conversations.syncAll(['allowed']);
```
{% endtab %}
{% endtabs %}

***

## Get started with the XMTP Node SDK

Use the [XMTP Node SDK](https://github.com/xmtp/xmtp-js/tree/main/sdks/node-sdk) to build agents and other server-side applications that interact with the XMTP network.

{% hint style="info" %}
**🤖 Building an agent?**

For building AI agents, use the [Build an agent with XMTP](https://docs.xmtp.org/agents/get-started/build-an-agent) tutorial tailored to this use case.
{% endhint %}

For all other server-side applications, including backends for chat apps, follow the get started guide below, which provides some quickstart code, as well as a map to building a [secure chat app](https://docs.xmtp.org/protocol/security) with XMTP, including support for:

* End-to-end encrypted direct message and group chat conversations
* Rich content types (attachments, reactions, replies, and more)
* Spam-free chats using user consent preferences

***

#### Quickstart

{% tabs %}
{% tab title="Node" %}
```javascript
// 1. Create an EOA or SCW signer.
// Details depend on your app's wallet implementation.
import type { Signer, Identifier, IdentifierKind } from '@xmtp/node-sdk';
 
const signer: Signer = {
  type: 'EOA',
  getIdentifier: () => ({
    identifier: '0x...', // Ethereum address as the identifier
    identifierKind: IdentifierKind.Ethereum,
  }),
  signMessage: async (message: string): Uint8Array => {
    // typically, signing methods return a hex string
    // this string must be converted to bytes and returned in this function
  },
};
 
// 2. Create the XMTP client
import { Client } from '@xmtp/node-sdk';
import { getRandomValues } from 'node:crypto';
 
const dbEncryptionKey = getRandomValues(new Uint8Array(32));
const client = await Client.create(signer, { dbEncryptionKey });
 
// 3. Start a new conversation
const group = await client.conversations.newGroup(
  [bo.inboxId, caro.inboxId],
  createGroupOptions /* optional */
);
 
// 4. Send messages
await group.send('Hello everyone');
 
// 5. List, stream, and sync
// List existing conversations
const allConversations = await client.conversations.list({
  consentStates: [ConsentState.Allowed],
});
// Stream new messages
const stream = await client.conversations.streamAllMessages({
  consentStates: [ConsentState.Allowed],
  onValue: (message) => {
    console.log('New message:', message);
  },
  onError: (error) => {
    console.error(error);
  },
});
 
// Or use for-await loop
for await (const message of stream) {
  // Received a message
  console.log('New message:', message);
}
// Sync all new welcomes, preference updates, conversations,
// and messages from allowed conversations
await client.conversations.syncAll(['allowed']);
```
{% endtab %}
{% endtabs %}

***

## Get started with the XMTP React Native SDK

Use the [XMTP React Native SDK](https://github.com/xmtp/xmtp-react-native) to build secure, private, and decentralized messaging into your cross-platform mobile app.

The guide provides some quickstart code, as well as a map to building a [secure chat app](https://docs.xmtp.org/protocol/security) with XMTP, including support for:

* End-to-end encrypted direct message and group chat conversations
* Rich content types (attachments, reactions, replies, and more)
* Spam-free chats using user consent preferences

***

#### Quickstart

{% tabs %}
{% tab title="React Native" %}
```javascript
// 1. Create an EOA or SCW signer.
// Details depend on your app's wallet implementation.
export function convertEOAToSigner(eoaAccount: EOAAccount): Signer {
  return {
    getIdentifier: async () =>
      new PublicIdentity(eoaAccount.address, 'ETHEREUM'),
    getChainId: () => undefined,
    getBlockNumber: () => undefined,
    signerType: () => 'EOA',
    signMessage: async (message: string) => ({
      signature: await eoaAccount.signMessage(message),
    }),
  };
}
 
// 2. Create the XMTP client
const client = Client.create(signer, {
  env: 'production',
  dbEncryptionKey: keyBytes, // 32 bytes
});
 
// 3. Start conversations
const group = await client.conversations.newGroup([bo.inboxId, caro.inboxId]);
const groupWithMeta = await client.conversations.newGroup(
  [bo.inboxId, caro.inboxId],
  {
    name: 'The Group Name',
    imageUrl: 'www.groupImage.com',
    description: 'The description of the group',
    permissionLevel: 'admin_only',
  }
);
 
// 4. Send messages
const dm = await client.conversations.findOrCreateDm(recipientInboxId);
await dm.send('Hello world');
await group.send('Hello everyone');
 
// 5. List, stream, and sync
// List existing conversations
await client.conversations.list(['allowed']);
// Stream new messages
await client.conversations.streamAllMessages(
  async (message: DecodedMessage<any>) => {
    // Received a message
  },
  { consentState: ['allowed'] }
);
// Sync all new welcomes, preference updates, conversations,
// and messages from allowed conversations
await client.conversations.syncAllConversations(['allowed']);
```
{% endtab %}
{% endtabs %}

***

## Get started with the XMTP Android SDK

Use the [XMTP Android SDK](https://github.com/xmtp/xmtp-android) to build secure, private, and decentralized messaging into your Android app.

The guide provides some quickstart code, as well as a map to building a [secure chat app](https://docs.xmtp.org/protocol/security) with XMTP, including support for:

* End-to-end encrypted direct message and group chat conversations
* Rich content types (attachments, reactions, replies, and more)
* Spam-free chats using user consent preferences

***

#### Quickstart

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// 1. Create an EOA or SCW signer.
// Details depend on your app's wallet implementation.
class EOAWallet : SigningKey {
    override val publicIdentity: PublicIdentity
        get() = PublicIdentity(IdentityKind.ETHEREUM, key.publicAddress)
    override val type: SignerType
        get() = SignerType.EOA
    override suspend fun sign(message: String): SignedData {
        return SignedData(key.sign(message = message))
    }
}
 
// 2. Create the XMTP client
val client = Client.create(
    account = SigningKey,
    options = ClientOptions(
        ClientOptions.Api(XMTPEnvironment.PRODUCTION, true),
        appContext = ApplicationContext(),
        dbEncryptionKey = keyBytes // 32 bytes
    )
)
 
// 3. Start conversations
val group = client.conversations.newGroup(inboxIds = listOf(bo.inboxId, caro.inboxId))
val groupWithMeta = client.conversations.newGroup(
    inboxIds = listOf(bo.inboxId, caro.inboxId),
    permissionLevel = GroupPermissionPreconfiguration.ALL_MEMBERS,
    name = "The Group Name",
    imageUrl = "www.groupImage.com",
    description = "The description of the group"
)
 
// 4. Send messages
val dm = client.conversations.findOrCreateDm(recipientInboxId)
dm.send(text = "Hello world")
group.send(text = "Hello everyone")
 
// 5. List, stream, and sync
// List existing conversations
val conversations = client.conversations.list()
val filteredConversations = client.conversations.list(consentState = ConsentState.ALLOWED)
// Stream all new conversations and new messages from allowed conversations
client.conversations.streamAllMessages(consentStates = listOf(ConsentState.ALLOWED)).collect {
    // Received a message
}
// Sync all new welcomes, preference updates, conversations,
// and messages from allowed conversations
client.conversations.syncAll(consentStates = ConsentState.ALLOWED)
```
{% endtab %}
{% endtabs %}

***

## Get started with the XMTP iOS SDK

Use the [XMTP iOS SDK](https://github.com/xmtp/xmtp-ios) to build secure, private, and decentralized messaging into your iOS app.

The guide provides some quickstart code, as well as a map to building a [secure chat app](https://docs.xmtp.org/protocol/security) with XMTP, including support for:

* End-to-end encrypted direct message and group chat conversations
* Rich content types (attachments, reactions, replies, and more)
* Spam-free chats using user consent preferences

***

#### Quickstart

{% tabs %}
{% tab title="Swift" %}
```swift
// 1. Create an EOA or SCW signer
// Details depend on your app's wallet implementation.
public struct EOAWallet: SigningKey {
    public var identity: PublicIdentity {
        return PublicIdentity(kind: .ethereum, identifier: key.publicAddress)
    }
    public var type: SignerType { .EOA }
    public func sign(message: String) async throws -> SignedData {
        return SignedData(try await key.sign(message: message))
    }
}
 
// 2. Create the XMTP client
let client = try await Client.create(
    account: SigningKey,
    options: ClientOptions.init(
        api: .init(env: .production, isSecure: true),
        dbEncryptionKey: keyBytes // 32 bytes
    )
)
 
// 3. Start conversations
let group = try await client.conversations.newGroup([bo.inboxId, caro.inboxId])
let groupWithMeta = try await client.conversations.newGroup([bo.inboxId, caro.inboxId],
    permissionLevel: .admin_only,
    name: "The Group Name",
    imageUrl: "www.groupImage.com",
    description: "The description of the group"
)
 
// 4. Send messages
let dm = try await client.conversations.findOrCreateDm(with: recipientInboxId)
try await dm.send(content: "Hello world")
try await group.send(content: "Hello everyone")
 
// 5. List, stream, and sync
// List existing conversations
let conversations = try await client.conversations.list()
let filteredConversations = try await client.conversations.list(consentState: .allowed)
// Stream new messages
for await message in try await client.conversations.streamAllMessages(type: /* OPTIONAL .dms, .groups, .all */, consentState: [.allowed]) {
    // Received a message
}
// Sync all new welcomes, preference updates, conversations,
// and messages from allowed conversations
try await client.conversations.syncAllConversations(consentState: [.allowed])
```
{% endtab %}
{% endtabs %}
