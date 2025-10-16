# Additional content types

## Support onchain transactions in your app built with XMTP

***

This package provides an [XMTP](https://docs.xmtp.org/) content type to support sending transactions to a wallet for execution.

Currently, this content type is supported in the Browser, Node, and React Native SDKs only.

For an example of an agent that implements the transaction content type, see [xmtp-transactions](https://github.com/ephemeraHQ/xmtp-agent-examples/tree/main/examples/xmtp-transactions).

{% hint style="info" %}
**Open for feedback**

You are welcome to provide feedback on this implementation by commenting on [XIP-59: Trigger on-chain calls via wallet\_sendCalls](https://community.xmtp.org/t/xip-59-trigger-on-chain-calls-via-wallet-sendcalls/889).
{% endhint %}

***

### Install the package

{% tabs %}
{% tab title="npm" %}
```sh
npm i @xmtp/content-type-wallet-send-calls
```
{% endtab %}

{% tab title="yarn" %}
```sh
yarn add @xmtp/content-type-wallet-send-calls
```
{% endtab %}

{% tab title="pnpm" %}
```sh
pnpm i @xmtp/content-type-wallet-send-calls
```
{% endtab %}
{% endtabs %}

***

### Configure the content type

After importing the package, you can register the codec.

{% tabs %}
{% tab title="Browser" %}
```javascript
import { WalletSendCallsCodec } from '@xmtp/content-type-wallet-send-calls';
// Create the XMTP client
const xmtp = await Client.create(signer, {
  env: 'dev',
  codecs: [new WalletSendCallsCodec()],
});
```
{% endtab %}
{% endtabs %}

***

### Create a transaction request

With [XMTP](https://docs.xmtp.org/), a transaction request is represented using `wallet_sendCalls` with additional metadata for display.

{% tabs %}
{% tab title="TypeScript" %}
```typescript
const walletSendCalls: WalletSendCallsParams = {
  version: '1.0',
  from: '0x123...abc',
  chainId: '0x2105',
  calls: [
    {
      to: '0x456...def',
      value: '0x5AF3107A4000',
      metadata: {
        description: 'Send 0.0001 ETH on base to 0x456...def',
        transactionType: 'transfer',
        currency: 'ETH',
        amount: 100000000000000,
        decimals: 18,
        toAddress: '0x456...def',
      },
    },
    {
      to: '0x789...cba',
      data: '0xdead...beef',
      metadata: {
        description: 'Lend 10 USDC on base with Morpho @ 8.5% APY',
        transactionType: 'lend',
        currency: 'USDC',
        amount: 10000000,
        decimals: 6,
        platform: 'morpho',
        apy: '8.5',
      },
    },
  ],
};
```
{% endtab %}
{% endtabs %}

***

### Send a transaction request

Once you have a transaction reference, you can send it as part of your conversation:

{% tabs %}
{% tab title="TypeScript" %}
```typescript
await conversation.messages.send(walletSendCalls, {
  contentType: ContentTypeWalletSendCalls,
});
```
{% endtab %}
{% endtabs %}

***

### Receive a transaction request

To receive and process a transaction request:

{% tabs %}
{% tab title="TypeScript" %}
```typescript
// Assume `loadLastMessage` is a thing you have
const message: DecodedMessage = await loadLastMessage();
 
if (!message.contentType.sameAs(ContentTypeWalletSendCalls)) {
  // Handle non-transaction request message
  return;
}
 
const walletSendCalls: WalletSendCallsParams = message.content;
// Process the transaction request here
```
{% endtab %}
{% endtabs %}
