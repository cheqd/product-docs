# 🤨 Troubleshooting Veramo CLI setup

This guide provides common errors and potential fixes for them if you encountered errors when [setting up Veramo CLI](setup-cli.md).

## Common errors when setting up Veramo CLI

### 1. Cannot find module `@cheqd/did-provider-cheqd`

```bash
$ veramo config check -f agent.yml

Unable to create agent from ./agent.yml. Error creating @cheqd/did-provider-cheqd['CheqdDIDProvider']: Cannot find module '@cheqd/did-provider-cheqd'
```

This error typically happens when the version of `did-provider-cheqd` is incompatible with the version of Veramo CLI being used.

This can happen if you _didn't_ use the `npm -g` ("global") flag when installing the packages, _and_ have a conflicting package version for any of the required packages in the folder where you executed this command from.

To verify if this is the case, check the version number of globally-installed NPM packages. Your list may be shorter or longer than this list, but pay close attention to the `@cheqd/did-provider-cheqd` and `@veramo/cli` versions.

```bash
$npm list -g

/usr/local/lib
├── @cheqd/did-provider-cheqd@1.6.5
├── @veramo/cli@3.1.6-next.160+6fbd22fa
└── npm@8.16.0
```

Compare and contrast this from the output from the _same_ folder where the command failed without the `--global` / `-g` flag:

```bash
$ npm list
@cheqd/did-provider-cheqd@1.6.5 /path/to/some/other/folder
├── @cheqd/sdk@1.2.8
├── @cheqd/ts-proto@1.0.12
├── ...
├── @veramo/cli@3.1.5
├── @veramo/core@3.1.6-next.160
├── @veramo/did-manager@3.1.6-next.160
├── @veramo/did-provider-key@3.1.3
├── @veramo/key-manager@3.1.6-next.160
├── ...
```

In this example above, you'll see that the version of the `@veramo/cli` package is _not_ the same one as the global one. This could equally happen with the `@cheqd/did-provider-cheqd` version.

Doing a global install for both packages will ensure that you've got the correct packages, regardless of which folder the `agent.yml` file is kept in.

### 2. Invalid word count in mnemonic

```bash
$ veramo config check -f agent.yml

Your Veramo configuration seems fine. An agent can be created and the 'agent.execute()' method can be called on it.
/usr/local/lib/node_modules/@cheqd/did-provider-cheqd/node_modules/@cosmjs/crypto/build/bip39.js:2137
            throw new Error(`Invalid word count in mnemonic (allowed: ${allowedWordsLengths} got: ${words.length})`);
```

This error is thrown if the `cosmosPayerMnemonic` value is set to an incorrect number of words (it should either be 12 or 24 words), or if it's left to the default value of `'your cosmos payer mnemonic'`.

_Note_: You can use the same mnemonic for both mainnet and testnet, since the same account address has separate balances on different networks.

_Heads-up_: Even if you're trying it on `testnet` you still need to put `cosmosPayerMnemonic` for your `mainnet` (You can use the same `cosmosPayerMnemonic`). Otherwise, you will still be getting the same error as above when you run `veramo config check -f agent.yml`.

### 3. Running `wget -c https://raw.githubusercontent.com/cheqd/did-provider-cheqd/main/agent.yml` command is giving you errors

- First, create `agent.yml` file at `root` directory level.

- Second, go to this [url](https://raw.githubusercontent.com/cheqd/did-provider-cheqd/main/agent.yml), copy everything from there and paste it in your `agent.yml` file. And you're good to go. 😉
