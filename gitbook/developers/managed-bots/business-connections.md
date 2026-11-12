---
description: "Let people connect your bot to their private chats so it can receive and answer messages on their behalf."
---

# Business connections

With Business connections, a person connects your bot to their own ARX account. Your bot then receives new messages in the private chats they choose and can reply on their behalf, for example with automatic replies while they are away. ARX shows their contacts that a bot assists them.

Business chats are end-to-end encrypted. Your bot receives encrypted envelopes and decrypts them with its own endpoint keys, which stay on your server. The SDKs include a runtime that holds those keys. Set it up with [Business endpoint](business-endpoint.md).

Turn on **Business connections** in [Advanced modes](../managed-bots.md) before people can connect your bot.

## What the person does <a href="#what-the-person-does" id="what-the-person-does"></a>

The person who connects your bot is the account owner. In ARX they:

1. Open your bot's profile and choose **Connect to my chats**.
2. Choose the scope: **All chats**, optionally with excluded chats, or **Only selected** chats.
3. Choose permissions. See [Permissions](#permissions).
4. Review your operator and data processors, then accept the connection.
5. Wait while ARX shows **Waiting for your bot to come online**. Your bot registers its endpoint in this step.
6. Approve your bot's certificate. They can compare the fingerprint with one you publish outside ARX.

The account owner manages connections in **Settings > Connected bots**. From there they can change scope and permissions, compare the fingerprint, approve a replacement certificate, or disconnect. Inside a chat, they can pause the bot, resume it or disable it for that chat.

The consent screen tells the account owner that:

- Your bot and its declared processors receive future messages in the chosen scope, without chat history.
- Their contacts see that a bot assists them.
- Disconnecting stops new access but does not erase data already delivered to your bot.

An account can have one connected bot at a time. All of the account owner's devices must run a supported ARX version, and a device that was added very recently must wait before it can approve a connection.

## What contacts see <a href="#what-contacts-see" id="what-contacts-see"></a>

Contacts in a connected chat see that the account owner is assisted by your bot. Messages your bot sends show the account owner's name with "via @yourbot". A contact can choose **Don't share my messages with this bot**. After that, your bot no longer receives that contact's messages.

## Permissions <a href="#permissions" id="permissions"></a>

The account owner grants each permission separately. `rights` in the connection object reports what they accepted.

| Permission in the app | Field | What it allows |
| --- | --- | --- |
| Read messages | `can_read_messages` | Mark messages as read. |
| Reply to messages | `can_reply` | Send replies within the reply window. |
| Delete messages sent by the bot | `can_delete_sent_messages` | Delete messages your bot sent through this connection. |
| Delete all messages | `can_delete_all_messages` | Delete any message in the permitted scope. |
| Send contacts | `can_send_contacts` | Include contact cards in replies. |
| Edit bio | `can_edit_bio` | Change the account owner's bio with [`business_account.bio.set`](../reference/business.md#operation-business-account-bio-set). |
| Edit username | `can_edit_username` | Change the account owner's username with [`business_account.username.set`](../reference/business.md#operation-business-account-username-set). |
| Edit profile photo | `can_edit_profile_photo` | Set or remove the account owner's profile photo with [`business_account.set_photo`](../reference/business.md#operation-business-account-set-photo) and [`business_account.delete_photo`](../reference/business.md#operation-business-account-delete-photo). |

The three profile permissions are optional. Only the account owner can grant them, and each one is `false` until the owner grants it. Check `rights` before you call the matching operation.

## What your bot receives <a href="#what-your-bot-receives" id="what-your-bot-receives"></a>

| Update type | When it arrives |
| --- | --- |
| `business_connection` | A person connects, approves a certificate, changes scope or permissions, pauses, or disconnects. |
| `business_envelope` | A new encrypted event in a connected chat. The runtime decrypts it into a typed update. |

A `business_connection` update contains a `BusinessConnection`:

| Field | Meaning |
| --- | --- |
| `id` | The connection ID, 22 to 128 base64url characters. |
| `user` | The account owner. |
| `user_chat_id` | A reserved ID for the account owner. It does not open a regular private chat; the owner must start your bot separately for that. |
| `connected_at` | When the account owner accepted the connection. |
| `rights` | The accepted permissions. |
| `status` | `pending_endpoint`, `pending_certificate`, `active`, `suspended`, `revoking` or `revoked`. |
| `is_enabled` | `true` when the connection is active and your endpoint and certificate are current. |
| `suspended_reason` | Why the connection is suspended, when available. |

Treat the update as a signal and read the current state with [`business_connections.get`](../reference/business.md#operation-business-connections-get) before you act on it.

| Status | What to do |
| --- | --- |
| `pending_endpoint` | Register your endpoint. The runtime's `register` call does this. |
| `pending_certificate` | Wait for the account owner to approve your certificate. They have at most 24 hours. |
| `active` | Receive envelopes and reply. |
| `suspended` | Stop sending. A connection is suspended when, for example, an endpoint reset is waiting for approval or one of the owner's devices is outdated. |
| `revoking`, `revoked` | Stop using the connection. Keep your endpoint state; do not reset keys. |

A `business_envelope` update carries `business_connection_id`, `envelope_id`, `chat` (with `kind` `e2e_business`), `direction`, `sender_user`, `sender_installation_token`, `event_class`, `ciphertext`, `type` and `received_at`. `direction` is `inbound` for messages from the contact and `owner_sync` for messages the account owner sends from their own devices. Repeated deliveries keep the same `envelope_id`.

After decryption, the runtime returns one of these typed updates:

| Typed update | Content |
| --- | --- |
| `business_message` | A new text message. `is_outgoing` is `true` for the account owner's own messages. |
| `edited_business_message` | An edited text message. |
| `deleted_business_messages` | The IDs of messages deleted for everyone. |

Other content, such as media or service events, is not decoded yet. The runtime rejects it, and the update stays unconfirmed until you skip it. See [Handle failures](business-endpoint.md#handle-failures).

## What your bot can send <a href="#what-your-bot-can-send" id="what-your-bot-can-send"></a>

The SDK runtime sends text replies, with an optional `reply_to` message ID, through [`business_envelopes.send`](../reference/business.md#operation-business-envelopes-send). Text can have 1 to 4,096 UTF-16 code units. Each reply is encrypted for the contact's devices and for every device of the account owner, so the owner sees what your bot sent.

Your bot can reply only when all of these hold:

- The connection is `active` and your endpoint and certificate are current.
- The account owner granted `can_reply`, and `can_send_contacts` for a contact card.
- The chat is in the connection's scope and is not paused.
- The contact has sent a message in that chat within the last 24 hours. Messages from the account owner do not reopen this window.

Your bot cannot read earlier history, start new chats, join groups as the account owner, or message people outside the scope.

## Read a connection <a href="#read-a-connection" id="read-a-connection"></a>

Read the current connection whenever a `business_connection` update arrives. This example prints its status and permissions.

{% tabs %}
{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const token = process.env.ARX_BOT_TOKEN;
const connectionId = process.env.CONNECTION_ID;
if (!token || !connectionId) throw new Error("Set ARX_BOT_TOKEN, CONNECTION_ID");
const bot = new Client({ token, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
	const connection = await bot.businessConnectionsGet(connectionId);
	console.log(connection.status, connection.is_enabled, connection.rights);
} finally {
	await bot.close();
}
```

{% endtab %}
{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(
    Config(
        token=os.environ["ARX_BOT_TOKEN"],
        base_url="https://signal.arx.pro/bot-api/v1",
    )
) as bot:
    connection = bot.business_connections_get(os.environ["CONNECTION_ID"])
    print(connection.status, connection.is_enabled, connection.rights)
```

{% endtab %}
{% tab title="Rust" %}

```rust
use arx_nexo::{Client, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("ARX_BOT_TOKEN")?;
    let connection_id = std::env::var("CONNECTION_ID")?;
    let bot = Client::new(Config::new(token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let connection = bot
        .business_connections_get(&connection_id, &[], None, None)
        .await?;
    println!("{:?} {} {:?}", connection.status, connection.is_enabled, connection.rights);
    Ok(())
}
```

{% endtab %}
{% endtabs %}

## Errors <a href="#errors" id="errors"></a>

These codes come from the Business methods. In TypeScript and Python, the runtime reports send failures as `BusinessRuntimeError` without details; Rust returns a typed `Error` variant for the most common ones.

| Code | HTTP | Meaning |
| --- | --- | --- |
| `business_connection_inactive` | 403 | The connection is not active. Read it again. |
| `business_endpoint_not_registered` | 409 | Register your endpoint first. |
| `business_right_missing` | 403 | The account owner did not grant the needed permission. `required_right` names it. |
| `business_chat_not_in_scope` | 403 | The chat is outside the scope or paused. |
| `business_recipient_not_allowed` | 403 | A copy is addressed to a device that is not an eligible recipient. The runtime reads current device lists before each send. |
| `business_reply_window_closed` | 403 | No message from the contact in the last 24 hours. |
| `business_peer_client_outdated` | 400 | One of the contact's devices runs an ARX version that is too old for Business messages. |
| `business_owner_copies_missing` | 400 | The send did not include every owner device. `details.expected` is the number of owner devices. The runtime includes them. |
| `business_event_type_not_allowed` | 403 | The event type is not supported. |
| `business_commitment_mismatch` | 400 | The device copies do not carry the same content. |
| `message_cant_be_deleted` | 403 | With `reason` `not_sent_by_bot`, the message was not sent by your bot. |
| `idempotency_key_reused` | 409 | The key was used with a different body. |
| `rate_limited` | 429 | Wait for `retry_after` seconds. |
| `capability_not_available` | 403 | Business connections is off for your bot, or Business devices cannot be read for this connection now. |

Do not retry these errors, except `rate_limited`, without a change in state. See [Errors](../errors.md) for the common error format.

## Related pages <a href="#related-pages" id="related-pages"></a>

- [Business endpoint](business-endpoint.md) sets up keys and sends automatic replies.
- [Advanced modes](../managed-bots.md) explains how to turn the mode on.
- [Privacy](../privacy.md) covers the data your bot handles.
