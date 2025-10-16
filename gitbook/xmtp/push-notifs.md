# Push notifications

## Understand push notifications with XMTP

***

With [XMTP](https://docs.xmtp.org/), you can enable real-time push notifications to keep users updated on new conversations and messages.

At the highest level, push notifications with [XMTP](https://docs.xmtp.org/) require these three elements:

1. An **XMTP push notification server** that listens for all messages sent on the [XMTP](https://docs.xmtp.org/) network. You set the server to listen to the`production`, `dev`, or `local` environment, and every message sent using that environment flows through the server. The server filters the messages accordingly and sends only the desired push notifications to a push notification service.
2. A **push notification service**, such as Apple Push Notification service (APNs), Firebase Cloud Messaging (FCM), or W3C Web Push protocol, receives push notifications from the [XMTP](https://app.gitbook.com/u/J3Qe4v65lSavsl2p6GHuZgLS4Mq1) push notification server.
3. An **app** that displays the push notifications.

***

### Understand message filtering

Let's dive deeper into how the [XMTP](https://docs.xmtp.org/) push notification server filters messages to determine which ones to send to the push notification service.

1. **Check if the server is subscribed to the message's topic**
   * A topic is a way to organize messages, and each message has a topic. To support push notifications, your app must [subscribe the server to the topics](https://docs.xmtp.org/chat-apps/push-notifs/push-notifs#subscribe-to-topics) that are relevant to your users. For example, for a user Alix, you must subscribe to all topics associated with Alix's conversations. The [XMTP](https://docs.xmtp.org/) push notification server has a list of these subscriptions. Your push notification server should expose functions to post the subscriptions to. The SDKs use protobufs as a universal language that allows the creation of these functions in any language. For example, [here are bufs](https://github.com/xmtp/xmtp-android/tree/main/library/src/main/java/org/xmtp/android/library/push) generated from the [XMTP example push notification server](https://docs.xmtp.org/chat-apps/push-notifs/pn-server). You can use these directly if you clone and use the example server.
   * If the arriving message's topic is **not on the list**, the server ignores it.
   * If the arriving message's topic is **on the list**, the server proceeds to check the message with the next filter.
2. **Check the `shouldPush` field value**
   * Each [content type](https://docs.xmtp.org/chat-apps/content-types/content-types), such as text, attachment, or reply, can have
   * A `shouldPush` boolean value is set at the content type level for each content type, such as text, attachment, or reply, so it can't be overwritten on send. By default, this value is set to _**true**_ for all content types except read receipts and reactions.
   * If the message's content type `shouldPush` value is _**false**_, the server ignores the message.
   * If the message's content type `shouldPush` value is _**true**_, the server proceeds to check the message with the next filter.
3. **Check the message's HMAC key**
   * Each message sent with [XMTP](https://docs.xmtp.org/) carries a single HMAC key. This key is updated with the encrypted message payload before being sent out.
   * If the message is signed by an HMAC key that **matches** the user's HMAC key, the push notification server ignores the message. This match means that the message was sent by the user themself, and they should not receive a push notification about a message they sent.
   * If the message is signed by an HMAC key that **does not match** the user's HMAC key, this means someone else sent the message and the user should be notified about it. At this point, the push notification server will send a notification.
4. **Send to the push notification service**
   * The server sends the message to the push notification service.
   * Once the push notification service has the message, it can format it appropriately for the push notification. This includes extracting necessary information, such as the sender's identity and message content, to craft meaningful notifications. This is only possible with the push notifications service inside the app and not with the push notification server because the server doesn't have the notion of a client and, therefore, can't decrypt the message.

[XMTP](https://docs.xmtp.org/) provides an example XMTP push notification server that implements the filtering described here. To learn more, see [Run a push notification server for an app built with XMTP](https://docs.xmtp.org/chat-apps/push-notifs/pn-server).
