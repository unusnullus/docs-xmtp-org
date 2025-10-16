# Core messaging

## List conversations

***

### List existing conversations

Get a list of existing group chat and DM conversations in the local database.

By default, `list` returns only conversations with a [consent state](https://docs.xmtp.org/chat-apps/user-consent/user-consent#how-user-consent-preferences-are-set) of allowed or unknown.

We recommend listing allowed conversations only. This ensures that spammy conversations with a consent state of unknown don't degrade the user experience.

To list all conversations regardless of consent state, use the `consentStates` option and pass all three consent states.

Conversations are listed in descending order by their `lastMessage` created at value. If a conversation has no messages, the conversation is ordered by its `createdAt` value.

{% tabs %}
{% tab title="Browser" %}
```javascript
const allConversations = await client.conversations.list({
  consentStates: [ConsentState.Allowed],
});
const allGroups = await client.conversations.listGroups({
  consentStates: [ConsentState.Allowed],
});
const allDms = await client.conversations.listDms({
  consentStates: [ConsentState.Allowed],
});
```
{% endtab %}

{% tab title="Node" %}
```python
const allConversations = await client.conversations.list({
  consentStates: [ConsentState.Allowed],
});
const allGroups = await client.conversations.listGroups({
  consentStates: [ConsentState.Allowed],
});
const allDms = await client.conversations.listDms({
  consentStates: [ConsentState.Allowed],
});
```
{% endtab %}

{% tab title="React Native" %}
<pre class="language-ruby"><code class="lang-ruby"><strong>// List Conversation items
</strong>await alix.conversations.list(['allowed']);
 
// List Conversation items and return only the fields set to true. Optimize data transfer
// by requesting only the fields the app needs.
await alix.conversations.list({
  members: false,
  consentState: false,
  description: false,
  creatorInboxId: false,
  addedByInboxId: false,
  isActive: false,
  lastMessage: true,
});
</code></pre>
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```
// List conversations (both groups and dms)
val conversations = alix.conversations.list()
val filteredConversations = client.conversations.list(consentState = ConsentState.ALLOWED)
 
// List just dms
val dms = alix.conversations.listDms()
val filteredDms = client.conversations.listDms(consentState = ConsentState.ALLOWED)
 
//List just groups
val groups = alix.conversations.listGroups()
val filteredGroups = client.conversations.listGroups(consentState = ConsentState.ALLOWED)
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```
// List conversations (both groups and dms)
let conversations = try await alix.conversations.list()
let orderFilteredConversations = try await client.conversations.list(consentState: .allowed)
 
// List just dms
let conversations = try await alix.conversations.listDms()
let orderFilteredConversations = try await client.conversations.listDms(consentState: .allowed)
 
//List just groups
let conversations = try await alix.conversations.listGroups()
let orderFilteredConversations = try await client.conversations.listGroups(consentState: .allowed)
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### List a user's active conversations

The `isActive()` method determines whether the current user is still an active member of a group conversation. For example:

* When a user is added to a group, `isActive()` returns `true` for that user
* When a user is removed from a group, `isActive()` returns `false` for that user

You can use a user's `isActive: true` value as a filter parameter when listing conversations. You can potentially have a separate section for "archived" or "inactive" conversations where you could use `isActive: false`.

***

## Stream conversations and messages

***

### List existing conversations

Get a list of existing group chat and DM conversations in the local database.

By default, `list` returns only conversations with a [consent state](https://docs.xmtp.org/chat-apps/user-consent/user-consent#how-user-consent-preferences-are-set) of allowed or unknown.

We recommend listing allowed conversations only. This ensures that spammy conversations with a consent state of unknown don't degrade the user experience.

To list all conversations regardless of consent state, use the `consentStates` option and pass all three consent states.

Conversations are listed in descending order by their `lastMessage` created at value. If a conversation has no messages, the conversation is ordered by its `createdAt` value.

{% tabs %}
{% tab title="Browser" %}
```javascript
const stream = await client.conversations.stream({
  onValue: (conversation) => {
    // Received a conversation
    console.log('New conversation:', conversation);
  },
  onError: (error) => {
    // Log any stream errors
    console.error(error);
  },
  onFail: () => {
    console.log('Stream failed');
  },
});
 
// Or use for-await loop
for await (const conversation of stream) {
  // Received a conversation
  console.log('New conversation:', conversation);
}
```
{% endtab %}

{% tab title="Node" %}
```python
const stream = await client.conversations.stream({
  onValue: (conversation) => {
    // Received a conversation
    console.log('New conversation:', conversation);
  },
  onError: (error) => {
    // Log any stream errors
    console.error(error);
  },
  onFail: () => {
    console.log('Stream failed');
  },
});
 
// To stream only groups
const groupStream = await client.conversations.streamGroups({
  onValue: (conversation) => {
    console.log('New group:', conversation);
  },
});
 
// To stream only DMs
const dmStream = await client.conversations.streamDms({
  onValue: (conversation) => {
    console.log('New DM:', conversation);
  },
});
 
// Or use for-await loop
for await (const conversation of stream) {
  // Received a conversation
  console.log('New conversation:', conversation);
}
```
{% endtab %}

{% tab title="React Native" %}
```ruby
await alix.conversations.stream(async (conversation: Conversation<any>) => {
  // Received a conversation
});
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```
alix.conversations.stream(type: /* OPTIONAL DMS, GROUPS, ALL */).collect {
  // Received a conversation
}
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```
for await convo in try await alix.conversations.stream(type: /* OPTIONAL .dms, .groups, .all */) {
  // Received a conversation
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### Stream new group chat and DM messages

This function listens to the network for new messages within all active group chats and DMs.

Whenever a new message is sent to any of these conversations, the callback is triggered with a `DecodedMessage` object. This keeps the inbox up to date by streaming in messages as they arrive.

By default, `streamAll` streams only conversations with a [consent state](https://docs.xmtp.org/chat-apps/user-consent/user-consent#how-user-consent-preferences-are-set) of allowed or unknown.

We recommend streaming messages for allowed conversations only. This ensures that spammy conversations with a consent state of unknown don't take up networking resources. This also ensures that unwanted spam messages aren't stored in the user's local database.

To stream all conversations regardless of consent state, pass `[Allowed, Unknown, Denied]`.

{% hint style="danger" %}
**Important**

The stream is infinite. Therefore, any looping construct used with the stream won't terminate unless you explicitly initiate the termination. You can initiate the termination by breaking the loop or by making an external call to `return`.
{% endhint %}

{% tabs %}
{% tab title="Browser" %}
```javascript
const stream = await client.conversations.stream({
  onValue: (conversation) => {
    // Received a conversation
    console.log('New conversation:', conversation);
  },
  onError: (error) => {
    // Log any stream errors
    console.error(error);
  },
  onFail: () => {
    console.log('Stream failed');
  },
});
 
// Or use for-await loop
for await (const conversation of stream) {
  // Received a conversation
  console.log('New conversation:', conversation);
}
```
{% endtab %}

{% tab title="Node" %}
```python
// stream all messages from conversations with a consent state of allowed
const stream = await client.conversations.streamAllMessages({
  consentStates: [ConsentState.Allowed],
  onValue: (message) => {
    // Received a message
    console.log('New message:', message);
  },
  onError: (error) => {
    // Log any stream errors
    console.error(error);
  },
  onFail: () => {
    console.log('Stream failed');
  },
});
 
// stream only group messages
const groupMessageStream = await client.conversations.streamAllGroupMessages({
  consentStates: [ConsentState.Allowed],
  onValue: (message) => {
    console.log('New group message:', message);
  },
});
 
// stream only dm messages
const dmMessageStream = await client.conversations.streamAllDmMessages({
  consentStates: [ConsentState.Allowed],
  onValue: (message) => {
    console.log('New DM message:', message);
  },
});
 
// Or use for-await loop
for await (const message of stream) {
  // Received a message
  console.log('New message:', message);
}
```
{% endtab %}

{% tab title="React Native" %}
```ruby
await alix.conversations.streamAllMessages(
  async (message: DecodedMessage<any>) => {
    // Received a message
  },
  { consentState: ['allowed'] }
);
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```
alix.conversations.streamAllMessages(type: /* OPTIONAL DMS, GROUPS, ALL */, consentState: listOf(ConsentState.ALLOWED)).collect {
  // Received a message
}
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```
for await message in try await alix.conversations.streamAllMessages(type: /* OPTIONAL .dms, .groups, .all */, consentState: [.allowed]) {
  // Received a message
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### Handle stream failures

{% hint style="danger" %}
**Browser and Node SDK**

Streams will automatically attempt to reconnect if they fail. By default, a stream will attempt to reconnect up to 6 times with a 10 second delay between each retry. To change these defaults, use the `retryAttempts` and `retryDelay` options. To disable this feature, set the `retryOnFail` option to `false`. During the retry process, the `onRetry` and `onRestart` callbacks can be used to monitor progress.
{% endhint %}

{% tabs %}
{% tab title="Browser" %}
```javascript
// Browser SDK also supports stream retry options
const stream = await client.conversations.streamAllMessages({
  consentStates: [ConsentState.Allowed],
  retryAttempts: 5,
  retryDelay: 15000, // 15 seconds
  onValue: (message) => {
    console.log('New message:', message);
  },
  onError: (error) => {
    console.error('Stream error:', error);
  },
  onFail: () => {
    console.log('Stream failed after retries');
  },
  onRestart: () => {
    console.log('Stream restarted');
  },
  onRetry: (attempt, maxAttempts) => {
    console.log(`Stream retry attempt ${attempt} of ${maxAttempts}`);
  },
});
```
{% endtab %}

{% tab title="Node" %}
```python
// disable automatic reconnects
const stream = await client.conversations.streamAllMessages({
  retryOnFail: false,
  onValue: (message) => {
    console.log('New message:', message);
  },
});
 
// use stream options with retry configuration
const stream = await client.conversations.streamAllMessages({
  consentStates: [ConsentState.Allowed],
  retryAttempts: 10,
  retryDelay: 20000, // 20 seconds
  onValue: (message) => {
    console.log('New message:', message);
  },
  onError: (error) => {
    console.error('Stream error:', error);
  },
  onFail: () => {
    console.log('Stream failed after retries');
  },
  onRestart: () => {
    console.log('Stream restarted');
  },
  onRetry: (attempt, maxAttempts) => {
    console.log(`Stream retry attempt ${attempt} of ${maxAttempts}`);
  },
});
```
{% endtab %}

{% tab title="React Native" %}
```ruby
const [messages, setMessages] = useState<DecodedMessage[]>([]);
 
const messageCallback = async (message: DecodedMessage<any>) => {
  setMessages((prev) => [...prev, message]);
};
const conversationFilterType: ConversationFilterType = 'all';
const consentStates: ConsentState[] = ['allowed'];
const onCloseCallback = () => {
  console.log('Message stream closed, handle retries here');
};
 
const startMessageStream = async () => {
  await alix.conversations.streamAllMessages(
    messageCallback,
    conversationFilterType,
    consentStates,
    onCloseCallback
  );
};
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```
private val _messages = MutableStateFlow<List<DecodedMessage>>(emptyList())
val messages: StateFlow<List<DecodedMessage>> = _messages.asStateFlow()
 
fun startMessageStream() {
    viewModelScope.launch {
        streamMessages(onClose = {
            Log.d("XMTP ViewModel", "Message stream closed.")
        }).collect { decodedMessage ->
            _messages.update { current ->
            current + decodedMessage
        }}
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```
@Published private(set) var messages: [DecodedMessage] = []
 
private var streamTask: Task<Void, Never>? = nil
 
func startMessageStream(from conversation: XMTPConversation) {
    streamTask?.cancel()
 
    streamTask = Task {
        do {
            for try await message in conversation.streamMessages(onClose: {
                print("XMTP ViewModel: Message stream closed.")
            }) {
                messages.append(message)
            }
        } catch {
            print("XMTP ViewModel: Stream failed with error \(error)")
        }
    }
}
 
func stopMessageStream() {
    streamTask?.cancel()
    streamTask = nil
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

## Sync conversations and messages

***

{% hint style="info" %}
**Note**

Syncing does not refetch existing conversations and messages. It also does not fetch messages for group chats you are no longer a part of.
{% endhint %}

***

### Sync a specific conversation

Get all new messages and group updates (name, description, etc.) for a specific conversation from the network.

{% tabs %}
{% tab title="Browser" %}
```javascript
await client.conversation.sync();
```
{% endtab %}

{% tab title="Node" %}
```python
await client.conversation.sync();
```
{% endtab %}

{% tab title="React Native" %}
```ruby
await client.conversation.sync();
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```
client.conversation.sync()
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```
try await client.conversation.sync()
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### Sync new conversations

Get any new group chat or DM conversations from the network.

{% tabs %}
{% tab title="Browser" %}
```javascript
await client.conversation.sync();
```
{% endtab %}

{% tab title="Node" %}
```python
await client.conversation.sync();
```
{% endtab %}

{% tab title="React Native" %}
```ruby
await client.conversation.sync();
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```
client.conversation.sync()
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```
try await client.conversation.sync()
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### Sync all new welcomes, conversations, messages, and preferences

Sync all new welcomes, group chat and DM conversations, messages, and [preference updates](https://docs.xmtp.org/chat-apps/list-stream-sync/sync-preferences) from the network.

By default, `syncAll` streams only conversations with a [consent state](https://docs.xmtp.org/chat-apps/user-consent/user-consent#how-user-consent-preferences-are-set) of allowed or unknown.

We recommend streaming messages for allowed conversations only. This ensures that spammy conversations with a consent state of unknown don't take up networking resources. This also ensures that unwanted spam messages aren't stored in the user's local database.

To sync all conversations regardless of consent state, pass `[ALLOWED, UNKNOWN, DENIED]`.

To sync preferences only, you can call [`preferences.sync`](https://docs.xmtp.org/chat-apps/list-stream-sync/sync-preferences). Note that `preferences.sync` will also sync welcomes to ensure that you have all potential new installations before syncing.

{% tabs %}
{% tab title="Browser" %}
```javascript
await client.conversations.syncAll(['allowed']);
```
{% endtab %}

{% tab title="Node" %}
```python
await client.conversations.syncAll(['allowed']);
```
{% endtab %}

{% tab title="React Native" %}
```ruby
await client.conversations.syncAllConversations(['allowed']);
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```
client.conversations.syncAllConversations(consentState = listOf(ConsentState.ALLOWED))
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```
try await client.conversations.syncAllConversations(consentState: [.allowed])
```
{% endcode %}
{% endtab %}
{% endtabs %}
