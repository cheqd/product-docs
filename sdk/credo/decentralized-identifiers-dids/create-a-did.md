---
description: Create a DID using the did:cheqd method from Credo Agent
---

# Create a DID

Follow these instructions to create a new DID from Credo Agent and publish the associated DID Document on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly configured [the Credo agent with cheqd](../setup-credo-agent.md).

## Create DID

### **Parameters**[**​**](https://credo.js.org/guides/tutorials/cheqd#parameters)

1. `method`\*: `cheqd`
2. `secret`
3. `options`\*
4. `didDocument`

### **Option 1**[**​**](https://credo.js.org/guides/tutorials/cheqd#option-1)

Provide a DID Document payload according to the w3c did core specification in the request body. This is possible when the keys corresponding to the verification methods provided in the DID Document are already created in the wallet

```typescript

// create a key pair
const key = await agent.wallet.createKey({
  keyType: KeyType.Ed25519,
})

// encode public key according to the verification method
const ed25519PublicKeyBase58 = key.publicKeyBase58

// Create a DID
await agent.dids.create<CheqdDidCreateOptions>({
  method: 'cheqd',
  secret: {},
  options: {},
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
    authentication: ['did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d#key-1'],
  }),
})
```

### **Option 2**[**​**](https://credo.js.org/guides/tutorials/cheqd#option-2)

If a DID Document is not passed to the registrar, it requires the secret parameter with a verificationMethod to construct the DID Document.

```typescript
await agent.dids.create({
  method: 'cheqd',
  // the secret contains a the verification method type and id
  secret: {
    verificationMethod: {
      id: 'key-1',
      type: 'Ed25519VerificationKey2020',
    },
  },
  // an optional methodSpecificIdAlgo parameter
  options: {
    network: 'testnet',
    methodSpecificIdAlgo: 'uuid',
  },
})
```



