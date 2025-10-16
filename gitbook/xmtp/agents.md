# Agents

## Build an agent

***

Use the [XMTP Agent SDK](https://github.com/xmtp/xmtp-js/tree/main/sdks/agent-sdk) to build agents that interact with the XMTP network.

***

### Installation

Install `@xmtp/agent-sdk` as a dependency in your project.

{% tabs %}
{% tab title="npm" %}
```sh
npm i @xmtp/agent-sdk
```
{% endtab %}

{% tab title="yarn" %}
```sh
yarn add @xmtp/agent-sdk
```
{% endtab %}

{% tab title="pnpm" %}
```sh
pnpm i @xmtp/agent-sdk
```
{% endtab %}

{% tab title="bun" %}
```sh
bun i @xmtp/agent-sdk
```
{% endtab %}
{% endtabs %}

***

### Usage

This example shows how to create an agent that sends a message when it receives a text message.

{% tabs %}
{% tab title="Node" %}
```javascript
import { Agent } from '@xmtp/agent-sdk';
import { getTestUrl } from '@xmtp/agent-sdk/debug';
 
// 2. Spin up the agent
const agent = await Agent.createFromEnv({
  env: 'dev', // or 'production'
});
 
// 3. Respond to text messages
agent.on('text', async (ctx) => {
  await ctx.sendText('Hello from my XMTP Agent! 👋');
});
 
// 4. Log when we're ready
agent.on('start', () => {
  console.log(`Waiting for messages...`);
  console.log(`Address: ${agent.address}`);
  console.log(`🔗 ${getTestUrl(agent.client)}`);
});
 
await agent.start();
```
{% endtab %}
{% endtabs %}

#### Local database

{% hint style="danger" %}
Each time you run your agent, XMTP creates local database files that must be **kept between restarts and between deployments**. Without persistent volumes, each restart creates a new installation and you're **limited to 10 installations per inbox**.
{% endhint %}

#### Set environment variables

To run an example XMTP agent, you must create a `.env` file with the following variables:

{% tabs %}
{% tab title="Shell" %}
```sh
XMTP_WALLET_KEY= # the private key of the wallet
XMTP_DB_ENCRYPTION_KEY= # encryption key for the local database
XMTP_ENV=dev # local, dev, production
```
{% endtab %}
{% endtabs %}

***

### Talk to your agent

Try out the example agents using [xmtp.chat](https://xmtp.chat), the official playground for agents.
