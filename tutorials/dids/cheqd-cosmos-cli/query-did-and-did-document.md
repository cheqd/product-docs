# Overview

This document is describing how to get already created DID-Doc from cheqd ledger.
We assumed, that there is already set up environment with operator's keys and etc. 

The next documents can be helpful for managing the DID-Docs:

- [Set up environment with docker](./environment-preps.md)
- [Created the DID-Doc](./create-did-and-did-document.md)
- [Update the DID-Doc](./update-and-manage-did-document.md)  

#### Querying a DID

For fetching the DID Document associated with an existing DID on cheqd networks the command can be used:

Command:

```
cheqd-noded query cheqd did <id> --chain-id <chain-id> --node <node-rpc-endpoint>
```

Example:

```
cheqd-noded query cheqd did did:cheqd:testnet:zJ5EDiiiKWDyo79n --chain-id cheqd-testnet-4 --node http://rpc.testnet.cheqd.network:26657