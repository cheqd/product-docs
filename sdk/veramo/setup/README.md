# Setting up Veramo CLI for cheqd

If you're looking to use the Veramo CLI with cheqd or develop a proof-of-concept application, use the [official Veramo CLI setup guide](https://veramo.io/docs/veramo\_agent/cli\_tool/).

## Step 1: Install requisite packages

_Node version recommended `Nodev16`_. You can install Node [here](https://nodejs.org/en/download/)

### 1.1. Install Veramo CLI

This step is exactly [as described in Veramo CLI docs](https://veramo.io/docs/veramo\_agent/cli\_tool/):

```bash
npm i @veramo/cli@latest -g
```

_Note:_ Depending on your system permissions, you might be prompted for additional permissions. Add `sudo` to the beginning of the command in case that happens.

Verify the installation was correct. Command below should output latest version of veramo you installed.

```bash
veramo -v
x.x.x
```

### 1.2. Install the `did-provider-cheqd` package

Install the `did-provider-cheqd` NPM package in a similar fashion:

```bash
npm install @cheqd/did-provider-cheqd@latest -g
```

You can check all of your NPM package versions by running the command:

```bash
npm list -g
```

## Step 2: Modify the cheqd plugin Agent configuration file

### 2.1. Get the `agent.yml` configuration file

Download the [`agent.yml`](https://raw.githubusercontent.com/cheqd/did-provider-cheqd/main/agent.yml) file that contains the configuration for cheqd network to be used with Veramo CLI.

You can do this in terminal through:

```bash
wget -c https://raw.githubusercontent.com/cheqd/did-provider-cheqd/main/agent.yml
```

_Note:_ Alternatively, you can also fetch this by [cloning](https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls) the [`did-provider-cheqd`](https://github.com/cheqd/did-provider-cheqd) repository.

### 2.2. Open the `agent.yml` file in an editor to customise the config

You can open the `agent.yml` in a text editor/IDE of your choice to edit a few mandatory settings.

In terminal, you can edit the `agent.yml` file using an editor like `nano`:

```bash
nano <path/to/>agent.yml
```

Make sure you provide the actual relative/absolute path to the file.

### 2.3. Generate a new local database encryption key

By default, the `did-provider-cheqd` package has a default SQLite database password, but it's a good idea to modify and change this to a new key unique to your install.

```bash
$ veramo config gen-key

X25519 raw private key (hex encoded):

4a5aeb56c7956dd6f3312e7094130a03afc060b95621fa3a86577aaf2b67cc1d

You can use this key with @veramo/kms-local#SecretBox
or replace the default agent.yml 'dbEncryptionKey' constant
```

Take the key generated and replace the value under `dbEncryptionKey` in the `agent.yml` file.

### 2.4. Set your DID Resolver endpoint

In order to be able to read/query `did:cheqd` entries from the ledger, you need to configure a REST API endpoint for a [cheqd DID Resolver](https://github.com/cheqd/did-resolver) instance.

```yaml
did-cheqd-resolver:
    $require: '@cheqd/did-provider-cheqd?t=function&p=/cheqd#getResolver'
    $args:
        - url: 'https://resolver.cheqd.net/1.0/identifiers/'
```

The default value is set to `resolver.cheqd.net`, which is an instance of the cheqd DID Resolver hosted by the cheqd team. This DID Resolver instance can handle requests for `did:cheqd:mainnet` as well as `did:cheqd:testnet` namespaces.

If you want, you can replace the `url` property with a different REST API endpoint for a different instance of the cheqd DID Resolver.

#### Alternative/Optional: Use Universal Resolver instead

> _Note:_ This configuration is an advanced step and **not recommended** for most users. Skip it and continue to the next step in most cases, unless you know why you want to switch the resolver interface.

If you plan on interacting with multiple DID methods using Veramo CLI, you can alternatively query `did:cheqd` using [a Universal Resolver instance](https://dev.uniresolver.io/) instead. This allows your CLI configuration to handle [_any_ DID method supported by Universal Resolver](https://github.com/decentralized-identity/universal-resolver).

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

### 2.5. Add cheqd-testnet to your Keplr wallet

In order to add cheqd-testnet to your Keplr extension, please follow the following instructions:

Go to [Axelar to add a custom network](https://docs.axelar.dev/resources/keplr#add-your-custom-network), then replace everything with cheqd-testnet json configuration below.

```json
{
    "chainId": "cheqd-testnet-6",
    "chainName": "cheqd Testnet",
    "rpc": "https://rpc.cheqd.network",
    "rest": "https://api.cheqd.network",
    "stakeCurrency": {
        "coinDenom": "CHEQ",
        "coinMinimalDenom": "ncheq",
        "coinDecimals": 9,
        "coinGeckoId": "cheqd-network"
    },
    "walletUrlForStaking": "https://wallet.cheqd.io",
    "bip44": {
        "coinType": 118
    },
    "bech32Config": {
        "bech32PrefixAccAddr": "cheqd",
        "bech32PrefixAccPub": "cheqdpub",
        "bech32PrefixValAddr": "cheqdvaloper",
        "bech32PrefixValPub": "cheqdvaloperpub",
        "bech32PrefixConsAddr": "cheqdvalcons",
        "bech32PrefixConsPub": "cheqdvalconspub"
    },
    "currencies": [
        {
            "coinDenom": "CHEQ",
            "coinMinimalDenom": "ncheq",
            "coinDecimals": 9,
            "coinGeckoId": "cheqd-network"
        }
    ],
    "feeCurrencies": [
        {
            "coinDenom": "CHEQ",
            "coinMinimalDenom": "ncheq",
            "coinDecimals": 9,
            "coinGeckoId": "cheqd-network",
            "gasPriceStep": {
                "low": 25,
                "average": 50,
                "high": 100
            }
        }
    ],
    "coinType": 118,
    "beta": true
}
```

### 2.6. Configure your cheqd/Cosmos account keys and RPC endpoints

While reading/querying from the cheqd ledger incurs no cost, if you want to [create/update a DID](../did-operations/) to cheqd ledger, you need to pay transaction fees for the ledger writes.

```yaml
# DID Manager
didManager:
---
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

1. `cosmosPayerMnemonic`: [Mnemonic associated with your cheqd/Cosmos SDK account](https://docs.cheqd.io/node/getting-started/cheqd-cli/cheqd-cli-key-management). This is only stored locally, and the mnemonic is used to reconstitute the account address and keys used to pay for the transaction.
2. `rpcUrl`: For both `did:cheqd:mainnet:` as well as `did:cheqd:testnet:` sections, you can specify a Cosmos SDK RPC endpoint. This endpoint is where transactions are sent to. By default, this is populated with `rpc.cheqd.net` (for _mainnet_) and `rpc.cheqd.network` (for _testnet_), but you can can modify this to [a different hosted RPC endpoint for cheqd](https://cosmos.directory/cheqd/nodes) or even your own local/private RPC endpoint.
3. `defaultProvider`: The default cheqd network is set to `did:cheqd:testnet` to allow developers to test out network functionality. However, if you prefer, you can switch this out to `did:cheqd:mainnet` instead.

### 2.7. Save the `agent.yml` file and exit

Make sure all your edits above are persisted and saved to a file that you can access.

## Step 3: Verify your configuration file is correct

Once you've completed Step 2 above, verify that your Veramo configuration is accurate using the following command. If your configuration is correct, you should get a success message like the one below.

```bash
$ veramo config check -f <path/to/>agent.yml

Your Veramo configuration seems fine. An agent can be created and the 'agent.execute()' method can be called on it.
```

> If the `config check` throws an error, check out our [troubleshooting guide for Veramo CLI setup](troubleshooting.md) to see common errors and fixes.

## Next steps

Now that your Veramo CLI installation is successfully set up to work with cheqd, try following our tutorials for [creating a new DID](../did-operations/create-did.md) or [querying existing DIDs](../did-operations/query-did.md).
