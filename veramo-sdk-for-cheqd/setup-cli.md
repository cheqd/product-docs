# 🧑‍💻🛠 Setting up Veramo SDK for cheqd

## Veramo Agent configuration steps

If you're looking to use the Veramo CLI with cheqd or develop a proof-of-concept application, use the [official Veramo CLI setup guide](https://veramo.io/docs/veramo_agent/cli_tool/).

### 1. Install Veramo CLI

This step is exactly [as described in Veramo CLI docs](https://veramo.io/docs/veramo_agent/cli_tool/):

```bash
npm i @veramo/cli@3.1.6-next.160 -g
```

> If you have stricter permissions when installing the package, prepend `sudo`.

i.e:

```bash
sudo npm install @veramo/cli@3.1.6-next.160 -g
```

### 2. Install the `did-provider-cheqd` package

Navigate to NPM global modules installation directory. Within unix operation systems, this generally lies at:

```bash
/usr/local/lib/node_modules
```

Once here, navigate to Veramo CLI global installation directory, using:

```bash
cd @veramo/cli
```

Finally, install the latest version of `did-provider-cheqd`, with:

```bash
npm install @cheqd/did-provider-cheqd@latest
```

### 3. Clone the `did-provider-cheqd` repo

Specifically, you need the [`agent.yml`](https://raw.githubusercontent.com/cheqd/did-provider-cheqd/main/agent.yml) file that contains the configuration for cheqd network.

If you execute commands from the directory where you have cloned this repo, then Veramo CLI will automatically detect the `agent.yml` file. Otherwise, you can specify the agent file as a parameter:

```bash
veramo <command> --config /path/to/agent.yml
```

> To check that you have the correct dependencies installed and your `agent.yml` file is configured correctly, use:

```bash
veramo config check -f /path/to/agent.yml
```

### 4. Generate a new local database encryption key

By default, the `did-provider-cheqd` package has a default SQLite database password, but it's a good idea to modify and change this to a new key unique to your install.

```bash
$ veramo config gen-key

X25519 raw private key (hex encoded):

4a5aeb56c7956dd6f3312e7094130a03afc060b95621fa3a86577aaf2b67cc1d

You can use this key with @veramo/kms-local#SecretBox
or replace the default agent.yml 'dbEncryptionKey' constant
```

Take the key generated and replace the value under `dbEncryptionKey` in the `agent.yml` file.

> To check that you have the correct dependencies installed and your `agent.yml` file is configured correctly, use:

```bash
veramo config check -f /path/to/agent.yml
```

### 5. Set your DID Resolver endpoint

In order to be able to read/query `did:cheqd` entries from the ledger, you need to configure a REST API endpoint for a [cheqd DID Resolver](https://github.com/cheqd/did-resolver) instance.

```yaml
did-cheqd-resolver:
  $require: '@cheqd/did-provider-cheqd?t=function&p=/cheqd#getResolver'
  $args:
    - url: 'https://resolver.cheqd.net/1.0/identifiers/'
```

The default value is set to `resolver.cheqd.net`, which is an instance of the cheqd DID Resolver hosted by the cheqd team. This DID Resolver instance can handle requests for `did:cheqd:mainnet` as well as `did:cheqd:testnet` namespaces.

If you want, you can replace the `url` property with a different REST API endpoint for a different instance of the cheqd DID Resolver.

#### Alternative: Use Universal Resolver instead

If you plan on interacting with multiple DID methods using Veramo CLI, you can alternatively query `did:cheqd` using [a Universal Resolver instance](https://dev.uniresolver.io/) instead. This allows your CLI configuration to handle [*any* DID method supported by Universal Resolver](https://github.com/decentralized-identity/universal-resolver).

Firstly, comment out the custom `did-cheqd-resolver` entry and uncomment the `universal-resolver` entry. This tells Veramo CLI to use the Universal Resolver interface for `did:cheqd`.

```yaml
# DID resolvers
didResolver:
  $require: '@veramo/did-resolver#DIDResolverPlugin'
  $args:
    - resolver:
        $require: did-resolver#Resolver
        $args:
          - key:
              $ref: /did-key-resolver
            # cheqd:
            #  $ref: /did-cheqd-resolver
            cheqd:
              $ref: /universal-resolver
```

Also comment out this section for the `did-cheqd-resolver`:

```yaml
# did-cheqd-resolver:
#   $require: '@cheqd/did-provider-cheqd?t=function&p=/cheqd#getResolver'
#   $args:
#     - url: 'https://resolver.cheqd.net/1.0/identifiers/'
```

Finally, uncomment and configure the `universal-resolver` interface:

```yaml
universal-resolver:
  $require: '@veramo/did-resolver#UniversalResolver'
  $args:
    - url: https://dev.uniresolver.io/1.0/identifiers/
```

> To check that you have the correct dependencies installed and your `agent.yml` file is configured correctly, use:

```bash
veramo config check -f /path/to/agent.yml
```

### 6. Configure your cheqd/Cosmos account keys and RPC endpoints

While reading/querying from the cheqd ledger incurs no cost, if you want to [create/update a DID](did-operations/README.md) to cheqd ledger, you need to pay transaction fees for the ledger writes.

```yaml
# DID Manager
didManager:
...
      defaultProvider: did:cheqd:testnet
      providers:
        did:cheqd:mainnet:
          $require: '@cheqd/did-provider-cheqd#CheqdDIDProvider'
          $args:
            - defaultKms: local
              cosmosPayerMnemonic: <your_cosmos_mnemonic_paying_for_did_txs>
              networkType: mainnet
              rpcUrl: 'https://rpc.cheqd.net'
        did:cheqd:testnet:
          $require: '@cheqd/did-provider-cheqd#CheqdDIDProvider'
          $args:
            - defaultKms: local
              cosmosPayerMnemonic: <your_cosmos_mnemonic_paying_for_did_txs>
              networkType: testnet
              rpcUrl: 'https://rpc.cheqd.network'
```

You need to configure this in under `didManager` section as shown above, where you'll need to edit:

1. `cosmosPayerMnemonic`: [Mnemonic associated with your cheqd/Cosmos SDK account](https://docs.cheqd.io/node/docs/cheqd-cli/cheqd-cli-key-management). This is only stored locally, and the mnemonic is used to reconstitute the account address and keys used to pay for the transaction.
2. `rpcUrl`: For both `did:cheqd:mainnet:` as well as `did:cheqd:testnet:` sections, you can specify a Cosmos SDK RPC endpoint. This endpoint is where transactions are sent to. By default, this is populated with `rpc.cheqd.net` (for *mainnet*) and `rpc.cheqd.network` (for *testnet*), but you can can modify this to [a different hosted RPC endpoint for cheqd](https://cosmos.directory/cheqd/nodes) or even your own local/private RPC endpoint.
3. `defaultProvider`: The default cheqd network is set to `did:cheqd:testnet` to allow developers to test out network functionality. However, if you prefer, you can switch this out to `did:cheqd:mainnet` instead.

> To check that you have the correct dependencies installed and your `agent.yml` file is configured correctly, use:

```bash
veramo config check -f /path/to/agent.yml
```

## Test your configuration

That completes the setup for cheqd's Veramo plugin! Try out and see if your configuration works by [querying a DID from ledger](did-operations/query-did.md).
