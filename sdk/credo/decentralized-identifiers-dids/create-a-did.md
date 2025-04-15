---
description: Create a DID using the did:cheqd method from Credo Agent
---

# Create a DID

This tutorial shows you how to create a **cheqd DID** using your configured **Credo Agent**, and how to publish the associated **DID Document** to the **cheqd ledger**.

## ⚠️ Prerequisites

Before you begin:

* ✅ Ensure your **Credo agent** is correctly configured with the `@credo-ts/cheqd` module
* ✅ You’ve set up a `cosmosPayerSeed` for publishing to the cheqd network
* ✅ You're connected to the correct network (`mainnet` or `testnet`)

## Method Overview

Credo supports two approaches to creating a DID:

#### Option 1 – Manually construct a full DID Document

Use when you want full control over the DID structure and already have key(s) in your wallet.

#### Option 2 – Auto-generate the DID Document

Use when you want Credo to create the DID Document from a key you specify in `secret`.

## Create DID

### **Parameters**[**​**](https://credo.js.org/guides/tutorials/cheqd#parameters)

1. `method`\*: `cheqd`
2. `secret`
3. `options`\*
4. `didDocument`

### Option 1: Create a DID from a full DID Document

1. First, generate a key pair:

```ts
const key = await agent.wallet.createKey({
  keyType: KeyType.Ed25519,
})

const ed25519PublicKeyBase58 = key.publicKeyBase58
```

2. Use that key to construct and register the DID Document:

```ts
import { DidDocument } from '@credo-ts/core'

await agent.dids.create<CheqdDidCreateOptions>({
  method: 'cheqd',
  secret: {}, // No secret needed if key is already in wallet
  options: {
    network: 'testnet',
  },
  didDocument: new DidDocument({
    id: 'did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d',
    controller: ['did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d'],
    verificationMethod: [
      {
        id: 'did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d#key-1',
        type: 'Ed25519VerificationKey2018',
        controller: 'did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d',
        publicKeyBase58: ed25519PublicKeyBase58,
      },
    ],
    authentication: [
      'did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d#key-1',
    ],
  }),
})
```

> 📝 Make sure the `publicKeyBase58` matches the key in your wallet.\
> The DID will be written to the `testnet` unless you specify `"mainnet"` in `options.network`.

### **Option 2**[**​**](https://credo.js.org/guides/tutorials/cheqd#option-2)

If you don’t want to manually build a DID Document, you can let Credo handle it based on your input key type and ID.

```ts
await agent.dids.create({
  method: 'cheqd',
  secret: {
    verificationMethod: {
      id: 'key-1', // Logical key name
      type: 'Ed25519VerificationKey2020', // Or another supported type
    },
  },
  options: {
    network: 'testnet',
    methodSpecificIdAlgo: 'uuid', // Optional: 'uuid' (default) or 'base58'
  },
})
```

> 🔐 Credo will generate the DID Document using the key referenced in `secret` and publish it to the network.

## What’s Next?

Now that your DID is live on the cheqd network, try:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create DID-Linked Resource</strong></mark></td><td>Associate DID-Linked Resources to your DID.</td></tr><tr><td><mark style="color:blue;"><strong>Issue Verifiable Credentials</strong></mark></td><td>Issue Credentials using your cheqd DID.</td></tr></tbody></table>
