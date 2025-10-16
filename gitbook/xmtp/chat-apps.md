# Signatures

## Use signatures with XMTP

***

With [XMTP](https://docs.xmtp.org/), you can use various types of signatures to sign and verify payloads.

***

### Sign with an external wallet

When a user creates, adds, removes, or revokes an [XMTP](https://docs.xmtp.org/) inbox's identity or installation, a signature is required.

***

### Sign with an XMTP key

You can sign something with [XMTP](https://docs.xmtp.org/) keys. For example, you can sign with [XMTP](https://docs.xmtp.org/) keys to send a payload to a backend.

{% tabs %}
{% tab title="Node" %}
```javascript
const signature = client.signWithInstallationKey(signatureText);
```
{% endtab %}

{% tab title="React Native" %}
```javascript
const signature = await client.signWithInstallationKey(signatureText);
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
val signature = client.signWithInstallationKey(signatureText)
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
let signature = try client.signWithInstallationKey(message: signatureText)
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### Verify with the same installation that signed

You can also sign with [XMTP](chat-apps.md#sign-with-an-external-wallet) keys and verify that a payload was sent by the same client.

{% tabs %}
{% tab title="Node" %}
```javascript
const signature = client.signWithInstallationKey(signatureText);
```
{% endtab %}

{% tab title="React Native" %}
```javascript
const isVerified = await client.verifySignature(signatureText, signature);
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
val isVerified = client.verifySignature(signatureText, signature)
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
let isVerified = try client.verifySignature(
            message: signatureText,
            signature: signature
        )
 
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

### Verify with the same inbox ID that signed

You can use an [XMTP](https://docs.xmtp.org/) key's `installationId` to create a signature, then pass both the signature and `installationId` to another `installationId` with the same `inboxId` to verify that the signature came from a trusted sender.

{% tabs %}
{% tab title="Node" %}
```javascript
const isValidSignature = client.verifySignedWithPrivateKey(
  signatureText,
  signature,
  installationId
);
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
val isVerified = client.verifySignatureWithInstallationId(
            signatureText,
            signature,
            installationId
      )
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
let isVerified = try client.verifySignatureWithInstallationId(
                message: signatureText,
                signature: signature,
                installationId: installationId
            )
```
{% endcode %}
{% endtab %}
{% endtabs %}
