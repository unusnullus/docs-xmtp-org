# User consents

## Understand how user consent preferences support spam-free chats

***

In any open and permissionless messaging ecosystem, spam is an inevitable reality, and XMTP is no exception.

However, with XMTP, you can give your users chats that are **spam-free spaces for chosen contacts only** by supporting user consent preferences.

***

### How user consent preferences work

With user consent preferences, an identity registered on the XMTP network can have one of three user consent preference values in relation to another user's identity:

* Unknown
* Allowed
* Denied

For example:

1. `alix.id` starts a conversation with `bo.id`. At this time, `alix.id` is unknown to `bo.id` and the conversation displays in a message requests UI.
2. When `bo.id` views the message request, they express their user consent preference to **Block** or **Accept** `alix.id` as a contact.
   * If `bo.id` accepts `alix.id` as a contact, their conversation displays in `bo.id`'s main inbox. Because only contacts `bo.id` accepts display in their main inbox, their inbox remains spam-free.
   * If `bo.id` blocks contact with `alix.id`, remove the conversation from `bo.id`'s view. In an appropriate location in your app, give the user the option to unblock the contact.

Your app should aim to handle consent preferences appropriately because they are an expression of user intent.

For example, if a user blocked a contact, your app should respect the user's intent to not see messages from the blocked contact. Handling the consent preference incorrectly and showing the user messages from the blocked contact may cause the user to lose trust in your app.

These user consent preferences are stored privately in an encrypted consent list on the XMTP network. The consent list is accessible by all apps that a user has authorized. This means a user can accept, or block, a contact once and have that consent respected across all other XMTP apps they use.

Be sure to load the latest consent list from the network at appropriate steps in your app flow to ensure that your app can operate using the latest data.

***

### How user consent preferences are set

Here are some of the ways user consent preferences are set:

#### Unknown

Conversation created in an app on an SDK version **with** user consent support:

* For a new conversation that a peer contact wants to start with a user, the consent preference is set to `unknown`.

Conversation created in an app on an SDK version **without** user consent support:

* For all conversations with any peer contact, the consent preference is set to `unknown`.

#### Allowed

Conversation created in an app on an SDK version **with** user consent support:

*   For a new conversation that a user created with a peer contact, the SDK sets the consent preference to `allowed`.

    The user's creation of the conversation with the contact is considered consent.
*   For an existing conversation created by a peer contact that hasn't had its consent preference updated on the network (`unknown`) and that the user responds to, the SDK will update the consent preference to `allowed`.

    The user's response to the conversation is considered consent.
* For a peer contact that a user has taken the action to allow, subscribe to, or enable notifications from, for example, the app must update the consent preference to `allowed`.

Conversation created in an app on an SDK version **without** user consent support:

* There are no scenarios in which a user consent preference will be set to `allowed`.

#### Denied

Conversation created in an app on an SDK version **with** user consent support:

* For a peer contact that a user has taken the action to block, unsubscribe from, or disable notifications from, for example, the app must update the consent preference to `denied`.

Conversation created in an app on an SDK version **without** user consent support:

* There are no scenarios in which a user consent preference will be set to `denied`.

***

### Sync new consent preferences from the network

You can sync new consent preferences (and HMAC keys) from the network using any of these calls:

* [Sync preferences only](https://docs.xmtp.org/chat-apps/list-stream-sync/sync-preferences)
* [Sync all new conversations, messages, and preferences](https://docs.xmtp.org/chat-apps/list-stream-sync/sync-and-syncall)
* [Stream all group chat and DM messages and preferences](https://docs.xmtp.org/chat-apps/list-stream-sync/stream)

***

### Get the consent state of a conversation

Check the current consent state of a specific conversation:

{% tabs %}
{% tab title="Browser" %}
```javascript
import { ConsentEntityType } from '@xmtp/browser-sdk';
 
// get consent state from the client
const conversationConsentState = await client.getConsentState(
  ConsentEntityType.GroupId,
  groupId
);
 
// or get consent state directly from a conversation
const groupConversation =
  await client.conversations.findConversationById(groupId);
const groupConversationConsentState = await groupConversation.consentState();
```
{% endtab %}

{% tab title="Node" %}
```javascript
import { ConsentEntityType } from '@xmtp/node-sdk';
 
// get consent state from the client
const conversationConsentState = await client.getConsentState(
  ConsentEntityType.GroupId,
  groupId
);
 
// or get consent state directly from a conversation
const groupConversation =
  await client.conversations.findConversationById(groupId);
const groupConversationConsentState = await groupConversation.consentState();
```
{% endtab %}

{% tab title="React Native" %}
```javascript
await conversation.consentState();
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
conversation.consentState()
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
try conversation.consentState()
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### Update the conversation consent state

Update the consent state of a conversation to allow or deny messages:

{% tabs %}
{% tab title="Browser" %}
```javascript
import { ConsentEntityType, ConsentState } from '@xmtp/browser-sdk';
 
// set consent state from the client (can set multiple states at once)
await client.setConsentStates([
  {
    entityId: groupId,
    entityType: ConsentEntityType.GroupId,
    state: ConsentState.Allowed,
  },
]);
 
// set consent state directly on a conversation
const groupConversation =
  await client.conversations.findConversationById(groupId);
await groupConversation.updateConsentState(ConsentState.Allowed);
```
{% endtab %}

{% tab title="Node" %}
```javascript
import { ConsentEntityType, ConsentState } from '@xmtp/node-sdk';
 
// set consent state from the client (can set multiple states at once)
await client.setConsentStates([
  {
    entityId: groupId,
    entityType: ConsentEntityType.GroupId,
    state: ConsentState.Allowed,
  },
]);
 
// set consent state directly on a conversation
const groupConversation =
  await client.conversations.findConversationById(groupId);
await groupConversation.updateConsentState(ConsentState.Allowed);
```
{% endtab %}

{% tab title="React Native" %}
```javascript
await conversation.updateConsent('allowed'); // 'allowed' | 'denied'
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
conversation.updateConsent(ALLOWED) // ALLOWED | DENIED
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
try await conversation.updateConsent(.allowed) // .allowed | .denied
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### Stream consent preferences in real-time

Listen for real-time updates to consent preferences:

{% tabs %}
{% tab title="Browser" %}
```javascript
// Stream consent records in real-time
const stream = await client.preferences.streamConsent({
  onValue: (updates) => {
    // Received consent updates
    console.log('Consent updates:', updates);
  },
  onError: (error) => {
    // Log any stream errors
    console.error(error);
  },
  onFail: () => {
    console.log('Consent stream failed');
  },
});
 
// Or use for-await loop
for await (const updates of stream) {
  // Received consent updates
  console.log('Consent updates:', updates);
}
```
{% endtab %}

{% tab title="Node" %}
```javascript
// Stream consent records in real-time
const stream = await client.preferences.streamConsent({
  onValue: (updates) => {
    // Received consent updates
    console.log('Consent updates:', updates);
  },
  onError: (error) => {
    // Log any stream errors
    console.error(error);
  },
  onFail: () => {
    console.log('Consent stream failed');
  },
});
 
// Or use for-await loop
for await (const updates of stream) {
  // Received consent updates
  console.log('Consent updates:', updates);
}
```
{% endtab %}

{% tab title="React Native" %}
```javascript
await client.preferences.streamConsent();
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
client.preferences.streamConsent().collect {
  // Received ConsentRecord
}
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
for await consent in try await client.preferences.streamConsent() {
  // Received consent
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### Update consent for an individual in a group chat

Update the consent state for an individual in a group chat:

{% hint style="info" %}
**Note**

You may want to enable users to deny or allow a users on an individual basis. You can then update the group chat UI to hide messages from denied individuals.
{% endhint %}

{% tabs %}
{% tab title="Browser" %}
```javascript
import { ConsentEntityType, ConsentState } from '@xmtp/browser-sdk';
 
await client.setConsentStates([
  {
    entityId: inboxId,
    entityType: ConsentEntityType.InboxId,
    state: ConsentState.Denied,
  },
]);
```
{% endtab %}

{% tab title="Node" %}
```javascript
import { ConsentEntityType, ConsentState } from '@xmtp/node-sdk';
 
// set consent state from the client (can set multiple states at once)
await client.setConsentStates([
  {
    entityId: inboxId,
    entityType: ConsentEntityType.InboxId,
    state: ConsentState.Denied,
  },
]);
```
{% endtab %}

{% tab title="React Native" %}
```javascript
await client.preferences.setConsentState(
  new ConsentRecord(inboxId, 'inbox_id', 'denied')
);
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
client.preferences.setConsentState(
    listOf(
        ConsentRecord(
            inboxId,
            EntryType.INBOX_ID,
            ConsentState.DENIED
        )
    )
)
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
try await client.preferences.setConsentState(
  entries: [
    ConsentRecord(
      value: inboxID,
      entryType: .inbox_id,
      consentType: .denied)
  ])
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### Get the consent state of an individual in a group chat

Get the consent state of an individual in a group chat:

{% hint style="info" %}
**Note**

You may want to enable users to deny or allow a users on an individual basis. You can then update the group chat UI to hide messages from denied individuals.
{% endhint %}

{% tabs %}
{% tab title="Browser" %}
```javascript
import { ConsentEntityType } from '@xmtp/browser-sdk';
 
const inboxConsentState = await client.getConsentState(
  ConsentEntityType.InboxId,
  inboxId
);
```
{% endtab %}

{% tab title="Node" %}
```javascript
import { ConsentEntityType } from '@xmtp/node-sdk';
 
const inboxConsentState = await client.getConsentState(
  ConsentEntityType.InboxId,
  inboxId
);
```
{% endtab %}

{% tab title="React Native" %}
```javascript
// Get consent directly on the member
const memberConsentStates = (await group.members()).map((member) =>
  member.consentState()
);
 
// Get consent from the inboxId
const inboxConsentState = await client.preferences.inboxIdConsentState(inboxId);
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
// Get consent directly on the member
val memberConsentStates = group.members().map { it.consentState }
 
// Get consent from the inboxId
val inboxConsentState = client.preferences.inboxIdState(inboxId)
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
// Get consent directly on the member
let memberConsentStates = try await group.members.map(\.consentState)
 
// Get consent from the inboxId
let inboxConsentState = try await client.preferences.inboxIdState(inboxId: inboxId)
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### See who created and added you to a group

Get the inbox ID of the individual who added you to a group or created the group to check the consent state for it:

{% tabs %}
{% tab title="React Native" %}
```javascript
group.addedByInboxId;
await group.creatorInboxId();
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
group.addedByInboxId()
group.creatorInboxId()
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
try await group.addedByInboxId()
try await group.creatorInboxId()
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### Handle unknown contacts

With user consent preferences, an inbox ID or conversation ID can have one of three user consent preference values in relation to another user's inbox ID:

* Unknown
* Allowed
* Denied

You can implement user consent preferences to give your users inboxes that are **spam-free spaces for allowed conversations and contacts only**.

You can then handle message requests from unknown contacts in a separate UI.

These message requests from unknown contacts could be from:

* Contacts the user might know
* Contacts the user might not know
* Spammy or scammy contacts

You can filter these unknown contacts to:

* Identify contacts the user might know or want to know and display them on a **You might know** tab, for example.
* Identify contacts the user might not know and not want to know, which might include spam, and display them on a **Hidden requests** tab, for example.

#### Identify contacts the user might know

To identify contacts the user might know or want to know, you can look for signals in onchain data that imply an affinity between addresses.

{% tabs %}
{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
val inboxState = inboxStateForInboxId(inboxId)
val identities = inboxState.identities
val ethAddresses = identities
    .filter { it.kind == ETHEREUM }
    .map { it.identifier }
```
{% endcode %}
{% endtab %}
{% endtabs %}

You can then display appropriate messages on a **You might know** tab, for example.

#### Identify contacts the user might not know, including spammy or scammy requests

To identify contacts the user might not know or not want to know, which might include spam, you can consciously decide to scan messages in an unencrypted state to find messages that might contain spammy or scammy content. You can also look for an absence of onchain interaction data between the addresses, which might indicate that there is no affinity between addresses. You can then filter the appropriate messages to display on a **Hidden requests** tab, for example.

The decision to scan unencrypted messages is yours as the app developer. If you take this approach:

* Handle unencrypted messages with extreme care, and don't store unencrypted messages beyond the time necessary to scan them.
* Consider telling users that your app scans unencrypted messages for spammy or scammy content.
* Consider making spam and scam message detection optional for users who prefer not to have their messages scanned.

#### Why is content moderation handled by apps and not XMTP?

XMTP is a decentralized, open protocol built to ensure private, secure, and censorship-resistant communication. As such, XMTP can't read unencrypted messages, and therefore, it also can't scan or filter message contents for spammy or scammy material.

The protocol can analyze onchain data signals, such as shared activity between wallet addresses, to infer potential affinities between addresses. However, because all XMTP repositories are open source, malicious actors could inspect these methods and develop workarounds to bypass them.

Additionally, applying spam filtering or content moderation directly at the protocol level would introduce centralization, which goes against the decentralized, permissionless, and open ethos of XMTP and web3. A protocol-driven approach could limit interoperability and trust by imposing subjective rules about content across all apps.

Instead, content filtering and moderation should be implemented at the app layer. Apps can decide how opinionated or lenient they want to be, tailoring their filtering approach to the needs of their users. For example, one app may choose to aggressively scan and block spam to provide a highly curated experience, attracting users who value more protection. Another app may opt for minimal or no filtering, appealing to users who prioritize full control and unfiltered communication.

This flexibility enables different apps to serve different user preferences, fostering an ecosystem where users can choose the experience that best suits them. Whether an app scans messages or not, XMTP ensures that developers remain free to build in line with their own values, without imposing restrictions at the infrastructure level. This separation between the protocol and app layers is crucial to maintaining XMTP's commitment to openness, interoperability, and user choice.

{% hint style="info" %}
Is your app using a great third-party or public good tool to help with spam and keep chats safe? Open an [issue](https://github.com/xmtp/docs-xmtp-org/issues) to share information about it.
{% endhint %}

