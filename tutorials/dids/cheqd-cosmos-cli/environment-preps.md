# Overview

This document describes the easiest way to up the environment and check the cheqd identity functionality.
For such purpose, `docker` is supposed to be as the main tool for getting such environment.

## Run docker container

Our target OS system is Ubuntu 20.04. For start playing the next command can help:

```bash
docker run -it --rm -u cheqd --entrypoint bash ghcr.io/cheqd/cheqd-node:0.4.0
```

The next step is to restore operator's keys:

```bash
cheqd-noded keys add <cheqd-operator-name> --recover --keyring-backend test
```

where, `cheqd-operator-name` it's name of alias for storing your keys locally, whatever you want.

## Example of working with a test account

For example, for test purposes let's create a key with alias `operator`:

```bash
~ docker run -it --rm -u cheqd ghcr.io/cheqd/cheqd-node:0.4.0 keys add operator --keyring-backend test
- name: operator
  type: local
  address: cheqd1vjuh4fjkcq0c02qullrt27z822gpn06sah2elh
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"A0N73o8ke8bp/7c7PgsRjHGddjHvk0USHwq+RDzwwE0t"}'
  mnemonic: ""
**Important** write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.
crawl field same drill indoor olympic tank lamp range olive announce during pact idea fall canal sauce film attend response mammal bounce stable suffer
```

The main bullets here:

* operator address: `cheqd1vjuh4fjkcq0c02qullrt27z822gpn06sah2elh`
* mnemonic phrase ( 24 words ): `crawl field same drill indoor olympic tank lamp range olive announce during pact idea fall canal sauce film attend response mammal bounce stable suffer`

Having this mnemonic phrase the user is able to restore their keys whenever they want. For continue playing a user needs to run:

```bash
~ docker run -it --rm -u cheqd --entrypoint bash ghcr.io/cheqd/cheqd-node:0.4.0
cheqd@8c3f88f653ab:~$ cheqd-noded keys add operator --recover --keyring-backend test
> Enter your bip39 mnemonic
crawl field same drill indoor olympic tank lamp range olive announce during pact idea fall canal sauce film attend response mammal bounce stable suffer
- name: operator
  type: local
  address: cheqd1vjuh4fjkcq0c02qullrt27z822gpn06sah2elh
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"A0N73o8ke8bp/7c7PgsRjHGddjHvk0USHwq+RDzwwE0t"}'
  mnemonic: ""
cheqd@8c3f88f653ab:~$ cheqd-noded keys list --keyring-backend test
- name: operator
  type: local
  address: cheqd1vjuh4fjkcq0c02qullrt27z822gpn06sah2elh
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"A0N73o8ke8bp/7c7PgsRjHGddjHvk0USHwq+RDzwwE0t"}'
  mnemonic: ""
cheqd@8c3f88f653ab:~$
```

As you can see, the recovered address is the same as was created before.

And after that all the commands from the [flow](https://github.com/cheqd/cheqd-node/blob/4b36c0a5f767b7d4fb91341bc732d54471702dac/docs/cheqd-cli/cheqd-cli-identity.md#demo-flow-for-sending-did-to-the-testnet) can be called.

P.S. the case with `docker` can be used only for demonstration purposes, cause after closing the container all the data will be lost. For production purposes, maybe it would be great to have an image with Ubuntu 20.04 and operator's keys inside. where `diddoc.json` - file with DID-Doc in json fromat.
