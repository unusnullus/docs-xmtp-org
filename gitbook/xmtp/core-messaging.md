# Core messaging

## Messaging implementation guideline

***

### Understanding of Client concept&#x20;

***

#### Create a EOA or SCW signer

XMTP SDKs support message signing with 2 different types of Ethereum accounts: Externally Owned Accounts (EOAs) and Smart Contract Wallets (SCWs). All SDK clients accept a signer object (or instance), which provides a method for signing messages.

***

#### Create an Externally Owned Account signer

The EOA signer must have 3 properties: the account type, a function that returns the account identifier, and a function that signs messages.

{% tabs %}
{% tab title="Browser" %}
```javascript
import type { Signer, Identifier } from '@xmtp/browser-sdk';
 
const accountIdentifier: Identifier = {
  identifier: '0x...', // Ethereum address as the identifier
  identifierKind: 'Ethereum', // Specifies the identity type
};
 
const signer: Signer = {
  type: 'EOA',
  getIdentifier: () => accountIdentifier,
  signMessage: async (message: string): Uint8Array => {
    // typically, signing methods return a hex string
    // this string must be converted to bytes and returned in this function
  },
};
```
{% endtab %}

{% tab title="Node" %}
```javascript
import type { Signer, Identifier, IdentifierKind } from '@xmtp/node-sdk';
 
const accountIdentifier: Identifier = {
  identifier: '0x...', // Ethereum address as the identifier
  identifierKind: IdentifierKind.Ethereum, // Specifies the identity type
};
 
const signer: Signer = {
  type: 'EOA',
  getIdentifier: () => accountIdentifier,
  signMessage: async (message: string): Uint8Array => {
    // typically, signing methods return a hex string
    // this string must be converted to bytes and returned in this function
  },
};
```
{% endtab %}

{% tab title="React Native" %}
```javascript
// Example EOA Signer
export function convertEOAToSigner(eoaAccount: EOAAccount): Signer {
  return {
    getIdentifier: async () =>
      new PublicIdentity(eoaAccount.address, 'ETHEREUM'),
    getChainId: () => undefined, // Provide a chain ID if available or return undefined
    getBlockNumber: () => undefined, // Block number is typically not available in Wallet, return undefined
    signerType: () => 'EOA', // "EOA" indicates an externally owned account
    signMessage: async (message: string) => {
      const signature = await eoaAccount.signMessage(message);
 
      return {
        signature,
      };
    },
  };
}
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
class EOAWallet : SigningKey {
    override val publicIdentity: PublicIdentity
      get() = PublicIdentity(
          IdentityKind.ETHEREUM,
          key.publicAddress
      )
    override val type: SignerType
      get() = SignerType.EOA
 
    override suspend fun sign(message: String): SignedData {
        val signature = key.sign(message = message)
        return SignedData(signature)
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
public struct EOAWallet: SigningKey {
    public var identity: PublicIdentity {
      return PublicIdentity(kind: .ethereum, identifier: key.publicAddress)
    }
 
    public var type: SignerType { .EOA }
 
    public func sign(message: String) async throws -> SignedData {
        let signature = try await key.sign(message: message)
        return SignedData(signature)
    }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

#### Create a Smart Contract Wallet signer

The SCW signer has the same 3 required properties as the EOA signer, but also requires a function that returns the chain ID of the blockchain being used and an optional function that returns the block number to verify signatures against. If a function is not provided to retrieve the block number, the latest block number will be used.

Here is a list of supported chain IDs:

* chain\_rpc\_1 = string
* chain\_rpc\_8453 = string
* chain\_rpc\_42161 = string
* chain\_rpc\_10 = string
* chain\_rpc\_137 = string
* chain\_rpc\_324 = string
* chain\_rpc\_59144 = string
* chain\_rpc\_480 = string

Need support for a different chain ID? Please post your request to the [XMTP Community Forums](https://community.xmtp.org/c/general/ideas/54).

The details of creating an SCW signer are highly dependent on the wallet provider and the library you're using to interact with it. Here are some general guidelines to consider:

* **Wallet provider integration**: Different wallet providers (Safe, Argent, Rainbow, etc.) have different methods for signing messages. See the wallet provider documentation for more details.
* **Library selection**: Choose a library that supports your wallet provider (e.g., viem, ethers.js, web3.js). Each library has its own API for interacting with wallets. See the library documentation for more details.
* **Add an Ethereum-specific prefix**: Before signing, Ethereum requires a specific prefix to be added to the message. To learn more, see [ERC-191: Signed Data Standard](https://eips.ethereum.org/EIPS/eip-191). Libraries and wallet providers might add the prefix for you, so make sure you don't add the prefix twice.
* **Hash the prefixed message with Keccak-256**: The prefixed message is hashed using the Keccak-256 algorithm, which is Ethereum's standard hashing algorithm. This step creates a fixed-length representation of the message, ensuring consistency and security. Note that some wallet providers might handle this hashing internally.
* **Sign the replay-safe hash**: The replay-safe hash is signed using the private key of the SCW. This generates a cryptographic signature that proves ownership of the wallet and ensures the integrity of the message.
* **Convert the signature to a Uint8Array**: The resulting signature is converted to a `Uint8Array` format, which is required by the XMTP SDK for compatibility and further processing.

The code snippets below are examples only and will need to be adapted based on your specific wallet provider and library.

{% tabs %}
{% tab title="Browser" %}
```javascript
export const createSCWSigner = (
  address: `0x${string}`,
  walletClient: WalletClient,
  chainId: bigint,
): Signer => {
  return {
    type: "SCW",
    getIdentifier: () => ({
      identifier: address.toLowerCase(),
      identifierKind: "Ethereum",
    }),
    signMessage: async (message: string) => {
      const signature = await walletClient.signMessage({
        account: address,
        message,
      });
      return toBytes(signature);
    },
    getChainId: () => {
      return chainId;
    },
  };
```
{% endtab %}

{% tab title="Node" %}
```javascript
import type { Signer, Identifier, IdentifierKind } from '@xmtp/node-sdk';
 
const accountIdentifier: Identifier = {
  identifier: '0x...', // Ethereum address as the identifier
  identifierKind: IdentifierKind.Ethereum, // Specifies the identity type
};
 
const signer: Signer = {
  type: 'SCW',
  getIdentifier: () => accountIdentifier,
  signMessage: async (message: string): Uint8Array => {
    // typically, signing methods return a hex string
    // this string must be converted to bytes and returned in this function
  },
  getChainId: () => BigInt(8453), // Example: Base chain ID
};
```
{% endtab %}

{% tab title="React Native" %}
```javascript
// Example SCW Signer
export function convertSCWToSigner(scwAccount: SCWAccount): Signer {
  return {
    getIdentifier: async () =>
      new PublicIdentity(scwAccount.address, 'ETHEREUM'),
    getChainId: () => 8453, // https://chainlist.org/
    getBlockNumber: () => undefined, // Optional: will be computed at runtime
    signerType: () => 'SCW', // "SCW" indicates smart contract wallet account
    signMessage: async (message: string) => {
      const byteArray = await scwAccount.signMessage(message);
      const signature = ethers.utils.hexlify(byteArray); // Convert to hex string
 
      return {
        signature,
      };
    },
  };
}
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
public struct SCWallet: SigningKey {
    public var identity: PublicIdentity {
      return PublicIdentity(kind: .ethereum, identifier: key.publicAddress)
    }
 
    public var chainId: Int64? {
        8453
    }
 
    public var blockNumber: Int64? {
        nil
    }
 
    public var type: SignerType { .SCW }
 
    public func sign(message: String) async throws -> SignedData {
        let signature = try await key.sign(message: message)
        return SignedData(signature.hexStringToByteArray )
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
public struct EOAWallet: SigningKey {
    public var identity: PublicIdentity {
      return PublicIdentity(kind: .ethereum, identifier: key.publicAddress)
    }
 
    public var type: SignerType { .EOA }
 
    public func sign(message: String) async throws -> SignedData {
        let signature = try await key.sign(message: message)
        return SignedData(signature)
    }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

#### Understand creating and building a client

When you call `Client.create()`, the following steps happen under the hood:

1. Extracts the `signer` and retrieves the wallet address from it.
2. Checks the XMTP identity ledger to find an inbox ID associated with the signer address. The inbox ID serves as the user's identity on the XMTP network.
   1. If it doesn't find an existing inbox ID, it requests a wallet signature to register the identity and create an inbox ID.
   2. If it finds an existing inbox ID, it uses the existing inbox ID.
3. Checks if a local SQLite database exists. This database contains the identity's installation state and message data.
   1. If it doesn't find an existing local database, it creates one. On non-web platforms, it encrypts the database with the provided `dbEncryptionKey`.
   2. If it finds an existing local database:
      * **For the Node, React Native, Android, and iOS SDKs**: It checks if the provided `dbEncryptionKey` matches. If it matches, it uses the existing database. If not, it creates a new database encrypted with the provided key.
      * **For the Browser SDK**: A `dbEncryptionKey` is not used for encryption due to technical limitations in web environments. Be aware that the database is not encrypted.
4. Returns the XMTP client, ready to send and receive messages.

The `dbEncryptionKey` client option is used by the Node, React Native, Android, and Swift SDKs only.

The encryption key is critical to the stability and continuity of an XMTP client. It encrypts the local SQLite database created when you call `Client.create()`, and must be provided every time you create or build a client.

As long as the local database and encryption key remain intact, you can use [`Client.build()`](https://docs.xmtp.org/chat-apps/core-messaging/create-a-client#build-an-existing-client) to rehydrate the same client without re-signing.

This encryption key is not stored or persisted by the XMTP SDK, so it's your responsibility as the app developer to store it securely and consistently.

If the encryption key is lost, rotated, or passed incorrectly during a subsequent `Client.create()` or `Client.build()` call (on non-web platforms), the app will be unable to access the local database. Likewise, if you initially provided the `dbPath` option, you must always provide it with every subsequent call or the client may be unable to access the database. The client will assume that the database can't be decrypted or doesn't exist, and will fall back to creating a new installation.

Creating a new installation requires a new identity registration and signature—and most importantly, **results in loss of access to all previously stored messages** unless the user has done a [history sync](https://docs.xmtp.org/chat-apps/list-stream-sync/history-sync).

To ensure seamless app experiences persist the `dbEncryptionKey` securely, and make sure it's available and correctly passed on each app launch

The `dbEncryptionKey` client option is not used by the Browser SDK for due to technical limitations in web environments. In this case, be aware that the database is not encrypted.

To learn more about database operations, see the [XMTP MLS protocol spec](https://github.com/xmtp/libxmtp/blob/main/xmtp_mls/README.md).

For debugging, it can be useful to decrypt a locally stored database. When a `dbEncryptionKey` is used, the XMTP client creates a [SQLCipher database](https://www.zetetic.net/sqlcipher/) which applies transparent 256-bit AES encryption. A `.sqlitecipher_salt` file is also generated alongside the database.

To open this database, you need to construct the password by prefixing `0x` (to indicate hexadecimal numbers), then appending the encryption key (64 hex characters, 32 bytes) and the salt (32 hex characters, 16 bytes). For example, if your encryption key is `A` and your salt is `B`, the resulting password would be `0xAB`.

The database also uses a [plaintext header size](https://www.zetetic.net/sqlcipher/sqlcipher-api/#cipher_plaintext_header_size) of 32 bytes.

If you want to inspect the database visually, you can use [DB Browser for SQLite](https://sqlitebrowser.org/), an open source tool that supports SQLite and SQLCipher. In its **Custom** encryption settings, set the **Plaintext Header Size** to _**32**_, and use the full **Password** as a **Raw key.**

***

#### Create a client

To call `Client.create()`, you must pass in a required `signer` and can also pass in any of the optional parameters covered in [Configure an XMTP client](https://docs.xmtp.org/chat-apps/core-messaging/create-a-client#configure-an-xmtp-client).

{% tabs %}
{% tab title="Browser" %}
```javascript
import { Client, type Signer } from '@xmtp/browser-sdk';
 
// create a signer
const signer: Signer = {
  /* ... */
};
 
const client = await Client.create(
  signer,
  // client options
  {
    // Note: dbEncryptionKey is not used for encryption in browser environments
  }
);
```
{% endtab %}

{% tab title="Node" %}
```javascript
import { Client, type Signer } from '@xmtp/node-sdk';
import { getRandomValues } from 'node:crypto';
 
// create a signer
const signer: Signer = {
  /* ... */
};
 
/**
 * The database encryption key is optional but strongly recommended for
 * secure local storage of the database.
 *
 * This value must be consistent when creating a client with an existing
 * database.
 */
const dbEncryptionKey = getRandomValues(new Uint8Array(32));
 
const client = await Client.create(
  signer,
  // client options
  {
    dbEncryptionKey,
    // Optional: Use a function to dynamically set the database path based on inbox ID
    // dbPath: (inboxId) => `./databases/xmtp-${inboxId}.db3`,
  }
);
```
{% endtab %}

{% tab title="React Native" %}
```javascript
Client.create(signer, {
  env: 'production', // 'local' | 'dev' | 'production'
  dbEncryptionKey: keyBytes, // 32 bytes
});
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
val options = ClientOptions(
    ClientOptions.Api(XMTPEnvironment.PRODUCTION, true),
    appContext = ApplicationContext(),
    dbEncryptionKey = keyBytes // 32 bytes
)
val client = Client().create(
        account = SigningKey,
        options = options
    )
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
let options = ClientOptions.init(
  api: .init(env: .production, isSecure: true),
  dbEncryptionKey: keyBytes // 32 bytes
)
let client = try await Client.create(
  account: SigningKey,
  options: options
)
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

#### Configure an XMTP client

You can configure an XMTP client with these options passed to `Client.create`:

{% tabs %}
{% tab title="Browser" %}
```javascript
import type { ContentCodec } from '@xmtp/content-type-primitives';
 
type ClientOptions = {
  /**
   * Specify which XMTP environment to connect to. (default: `dev`)
   */
  env?: 'local' | 'dev' | 'production';
  /**
   * Add a client app version identifier that's included with API requests.
   * Production apps are strongly encouraged to set this value.
   *
   * You can use the following format: `appVersion: 'APP_NAME/APP_VERSION'`.
   *
   * For example: `appVersion: 'alix/2.x'`
   *
   * If you have an app and an agent, it's best to distinguish them from each other by
   * adding `-app` and `-agent` to the names. For example:
   *
   * - App: `appVersion: 'alix-app/3.x'`
   * - Agent: `appVersion: 'alix-agent/2.x'`
   *
   * Setting this value provides telemetry that shows which apps are using the
   * XMTP client SDK. This information can help XMTP core developers provide you with app
   * support, especially around communicating important SDK updates, deprecations,
   * and required upgrades.
   */
  appVersion?: string;
  /**
   * apiUrl can be used to override the `env` flag and connect to a
   * specific endpoint
   */
  apiUrl?: string;
  /**
   * historySyncUrl can be used to override the `env` flag and connect to a
   * specific endpoint for syncing history
   */
  historySyncUrl?: string | null;
  /**
   * Allow configuring codecs for additional content types
   */
  codecs?: ContentCodec[];
  /**
   * Path to the local DB
   *
   * There are 4 value types that can be used to specify the database path:
   *
   * - `undefined` (or excluded from the client options)
   *    The database will be created in the current working directory and is based on
   *    the XMTP environment and client inbox ID.
   *    Example: `xmtp-dev-<inbox-id>.db3`
   *
   * - `null`
   *    No database will be created and all data will be lost once the client disconnects.
   *
   * - `string`
   *    The given path will be used to create the database.
   *    Example: `./my-db.db3`
   *
   * - `function`
   *    A callback function that receives the inbox ID and returns a string path.
   *    Example: `(inboxId) => string`
   */
  dbPath?: string | null | ((inboxId: string) => string);
  /**
   * Encryption key for the local DB
   */
  dbEncryptionKey?: Uint8Array;
  /**
   * Enable structured JSON logging
   */
  structuredLogging?: boolean;
  /**
   * Enable performance metrics
   */
  performanceLogging?: boolean;
  /**
   * Logging level
   */
  loggingLevel?: 'off' | 'error' | 'warn' | 'info' | 'debug' | 'trace';
  /**
   * Disable automatic registration when creating a client
   */
  disableAutoRegister?: boolean;
  /**
   * Disable device sync
   */
  disableDeviceSync?: boolean;
};
```
{% endtab %}

{% tab title="Node" %}
```javascript
import type { ContentCodec } from '@xmtp/content-type-primitives';
import type { LogLevel } from '@xmtp/node-bindings';
 
type ClientOptions = {
  /**
   * Specify which XMTP environment to connect to. (default: `dev`)
   */
  env?: 'local' | 'dev' | 'production';
  /**
   * Add a client app version identifier that's included with API requests.
   * Production apps are strongly encouraged to set this value.
   *
   * You can use the following format: `appVersion: 'APP_NAME/APP_VERSION'`.
   *
   * For example: `appVersion: 'alix/2.x'`
   *
   * If you have an app and an agent, it's best to distinguish them from each other by
   * adding `-app` and `-agent` to the names. For example:
   *
   * - App: `appVersion: 'alix-app/3.x'`
   * - Agent: `appVersion: 'alix-agent/2.x'`
   *
   * Setting this value provides telemetry that shows which apps are using the
   * XMTP client SDK. This information can help XMTP core developers provide you with app
   * support, especially around communicating important SDK updates, deprecations,
   * and required upgrades.
   */
  appVersion?: string;
  /**
   * apiUrl can be used to override the `env` flag and connect to a
   * specific endpoint
   */
  apiUrl?: string;
  /**
   * historySyncUrl can be used to override the `env` flag and connect to a
   * specific endpoint for syncing history
   */
  historySyncUrl?: string | null;
  /**
   * Path to the local DB
   *
   * There are 4 value types that can be used to specify the database path:
   *
   * - `undefined` (or excluded from the client options)
   *    The database will be created in the current working directory and is based on
   *    the XMTP environment and client inbox ID.
   *    Example: `xmtp-dev-<inbox-id>.db3`
   *
   * - `null`
   *    No database will be created and all data will be lost once the client disconnects.
   *
   * - `string`
   *    The given path will be used to create the database.
   *    Example: `./my-db.db3`
   *
   * - `function`
   *    A callback function that receives the inbox ID and returns a string path.
   *    Example: `(inboxId) => string`
   */
  dbPath?: string | null | ((inboxId: string) => string);
  /**
   * Encryption key for the local DB
   */
  dbEncryptionKey?: Uint8Array;
  /**
   * Allow configuring codecs for additional content types
   */
  codecs?: ContentCodec[];
  /**
   * Enable structured JSON logging
   */
  structuredLogging?: boolean;
  /**
   * Logging level
   */
  loggingLevel?: LogLevel;
  /**
   * Disable automatic registration when creating a client
   */
  disableAutoRegister?: boolean;
  /**
   * Disable device sync
   */
  disableDeviceSync?: boolean;
};
```
{% endtab %}

{% tab title="React Native" %}
```javascript
import type { ContentCodec } from '@xmtp/react-native-sdk';
 
type ClientOptions = {
  /**
   * Specify which XMTP environment to connect to. (default: `dev`)
   */
  env: 'local' | 'dev' | 'production';
  /**
   * Add a client app version identifier that's included with API requests.
   * Production apps are strongly encouraged to set this value.
   *
   * You can use the following format: `appVersion: 'APP_NAME/APP_VERSION'`.
   *
   * For example: `appVersion: 'alix/2.x'`
   *
   * If you have an app and an agent, it's best to distinguish them from each other by
   * adding `-app` and `-agent` to the names. For example:
   *
   * - App: `appVersion: 'alix-app/3.x'`
   * - Agent: `appVersion: 'alix-agent/2.x'`
   *
   * Setting this value provides telemetry that shows which apps are using the
   * XMTP client SDK. This information can help XMTP core developers provide you with app
   * support, especially around communicating important SDK updates, deprecations,
   * and required upgrades.
   */
  appVersion?: string;
  /**
   * REQUIRED specify the encryption key for the database. The encryption key must be exactly 32 bytes.
   */
  dbEncryptionKey: Uint8Array;
  /**
   * Set optional callbacks for handling identity setup
   */
  preAuthenticateToInboxCallback?: () => Promise<void> | void;
  /**
   * OPTIONAL specify the XMTP managed database directory
   */
  dbDirectory?: string;
  /**
   * OPTIONAL specify a url to sync message history from
   */
  historySyncUrl?: string;
  /**
   * OPTIONAL specify a custom local host for testing on physical devices for example `localhost`
   */
  customLocalHost?: string;
  /**
   * Allow configuring codecs for additional content types
   */
  codecs?: ContentCodec[];
};
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
import android.content.Context
 
typealias PreEventCallback = suspend () -> Unit
 
data class ClientOptions(
    val api: Api = Api(),
    val preAuthenticateToInboxCallback: PreEventCallback? = null,
    val appContext: Context,
    val dbEncryptionKey: ByteArray,
    val historySyncUrl: String? = when (api.env) {
        XMTPEnvironment.PRODUCTION -> "https://message-history.production.ephemera.network/"
        XMTPEnvironment.LOCAL -> "http://0.0.0.0:5558"
        else -> "https://message-history.dev.ephemera.network/"
    },
    val dbDirectory: String? = null,
) {
    data class Api(
        val env: XMTPEnvironment = XMTPEnvironment.DEV,
        val isSecure: Boolean = true,
        /**
         * Add a client app version identifier that's included with API requests.
         * Production apps are strongly encouraged to set this value.
         *
         * You can use the following format: `appVersion: "APP_NAME/APP_VERSION"`.
         *
         * For example: `appVersion: 'alix/2.x'`
         *
         * If you have an app and an agent, it's best to distinguish them from each other by
         * adding `-app` and `-agent` to the names. For example:
         *
         * - App: `appVersion: 'alix-app/3.x'`
         * - Agent: `appVersion: 'alix-agent/2.x'`
         *
         * Setting this value provides telemetry that shows which apps are using the
         * XMTP client SDK. This information can help XMTP core developers provide you
         * with app support, especially around communicating important SDK updates,
         * deprecations, and required upgrades.
         */
        val appVersion: String? = null,
    )
}
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
import LibXMTP
 
public struct ClientOptions {
 // Specify network options
 public struct Api {
  /// Specify which XMTP network to connect to. Defaults to ``.dev``
  public var env: XMTPEnvironment = .dev
 
  /// Specify whether the API client should use TLS security. In general this should only be false when using the `.local` environment.
  public var isSecure: Bool = true
 
  /// Add a client app version identifier that's included with API requests.
  /// Production apps are strongly encouraged to set this value.
  ///
  /// You can use the following format: `appVersion: "APP_NAME/APP_VERSION"`.
  ///
  /// For example: `appVersion: 'alix/2.x'`
  ///
  /// If you have an app and an agent, it's best to distinguish them from each other by
  /// adding `-app` and `-agent` to the names. For example:
  /// - App: `appVersion: 'alix-app/3.x'`
  /// - Agent: `appVersion: 'alix-agent/2.x'`
  ///
  /// Setting this value provides telemetry that shows which apps are using the
  /// XMTP client SDK. This information can help XMTP core developers provide you
  //  with app support, especially around communicating important SDK updates,
  /// deprecations, and required upgrades.
  public var appVersion: String?
 }
 
 public var api = Api()
 public var codecs: [any ContentCodec] = []
 
 /// `preAuthenticateToInboxCallback` will be called immediately before an Auth Inbox signature is requested from the user
 public var preAuthenticateToInboxCallback: PreEventCallback?
 
 public var dbEncryptionKey: Data
 public var dbDirectory: String?
 public var historySyncUrl: String?
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

#### Log out a client

When you log a user out of your app, you can give them the option to delete their local database.

{% hint style="info" %}
**Important**

If the user chooses to delete their local database, they will lose all of their messages and will have to create a new installation the next time they log in.
{% endhint %}

{% tabs %}
{% tab title="Browser" %}
```javascript
/**
 * The Browser SDK client does not currently support deleting the local database.
 */
 
// this method only terminates the client's associated web worker
client.close();
```
{% endtab %}

{% tab title="Node" %}
```javascript
/**
 * The Node SDK client does not have a method to delete the local database.
 * Simply delete the local database file from the file system.
 */
```
{% endtab %}

{% tab title="React Native" %}
```javascript
await client.deleteLocalDatabase();
await Client.dropClient(client.installationId);
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
client.deleteLocalDatabase()
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
try await client.deleteLocalDatabase()
```
{% endcode %}
{% endtab %}
{% endtabs %}

### List conversations

***

#### List existing conversations

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

{% tab title="React Native" %}
<pre class="language-javascript"><code class="lang-javascript"><strong>// List Conversation items
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
```kotlin
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
```swift
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

#### List a user's active conversations

The `isActive()` method determines whether the current user is still an active member of a group conversation. For example:

* When a user is added to a group, `isActive()` returns `true` for that user
* When a user is removed from a group, `isActive()` returns `false` for that user

You can use a user's `isActive: true` value as a filter parameter when listing conversations. You can potentially have a separate section for "archived" or "inactive" conversations where you could use `isActive: false`.

***

### Stream conversations and messages

***

#### List existing conversations

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
```javascript
await alix.conversations.stream(async (conversation: Conversation<any>) => {
  // Received a conversation
});
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
alix.conversations.stream(type: /* OPTIONAL DMS, GROUPS, ALL */).collect {
  // Received a conversation
}
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
for await convo in try await alix.conversations.stream(type: /* OPTIONAL .dms, .groups, .all */) {
  // Received a conversation
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

#### Stream new group chat and DM messages

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
```javascript
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
```javascript
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
```kotlin
alix.conversations.streamAllMessages(type: /* OPTIONAL DMS, GROUPS, ALL */, consentState: listOf(ConsentState.ALLOWED)).collect {
  // Received a message
}
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
for await message in try await alix.conversations.streamAllMessages(type: /* OPTIONAL .dms, .groups, .all */, consentState: [.allowed]) {
  // Received a message
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

#### Handle stream failures

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
```javascript
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
```javascript
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
```kotlin
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
```swift
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

### Sync conversations and messages

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
```javascript
await client.conversation.sync();
```
{% endtab %}

{% tab title="React Native" %}
```javascript
await client.conversation.sync();
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
client.conversation.sync()
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
try await client.conversation.sync()
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

#### Sync new conversations

Get any new group chat or DM conversations from the network.

{% tabs %}
{% tab title="Browser" %}
```javascript
await client.conversation.sync();
```
{% endtab %}

{% tab title="Node" %}
```javascript
await client.conversation.sync();
```
{% endtab %}

{% tab title="React Native" %}
```javascript
await client.conversation.sync();
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
client.conversation.sync()
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
try await client.conversation.sync()
```
{% endcode %}
{% endtab %}
{% endtabs %}

***

#### Sync all new welcomes, conversations, messages, and preferences

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
```javascript
await client.conversations.syncAll(['allowed']);
```
{% endtab %}

{% tab title="React Native" %}
```javascript
await client.conversations.syncAllConversations(['allowed']);
```
{% endtab %}

{% tab title="Kotlin" %}
{% code title="" %}
```kotlin
client.conversations.syncAllConversations(consentState = listOf(ConsentState.ALLOWED))
```
{% endcode %}
{% endtab %}

{% tab title="Swift" %}
{% code title="" %}
```swift
try await client.conversations.syncAllConversations(consentState: [.allowed])
```
{% endcode %}
{% endtab %}
{% endtabs %}
