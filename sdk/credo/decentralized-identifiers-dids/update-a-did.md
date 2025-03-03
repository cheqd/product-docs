---
description: Update a did:cheqd DID from Credo Agent
---

# Update a DID

Follow these instructions to update a did:cheqd DID from Credo Agent.

## Update DID

To update a DID Document, fetch the body of the DID Document you want to change from the DID Resolver, make the relevant updates and pass it as the parameter

### **Parameters**[**​**](https://credo.js.org/guides/tutorials/cheqd#parameters-1)

1. `did`\*
2. `didDocument`\*: The updated DID Document
3. `options`
4. `secret`

```typescript
await agent.dids.update({
  did: 'did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411',
  // Updates DID Document with an additional verification method if provided
  secret: {
    verificationMethod: {
      id: 'key-2',
      type: 'JsonWebKey2020',
    },
  },
  didDocument: {
    id: 'did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411',
    controller: ['did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411'],
    verificationMethod: [
      {
        id: 'did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411#key-1',
        type: 'Ed25519VerificationKey2020',
        controller: 'did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411',
        publicKeyMultibase: 'z6MknkzLUEP5cxqqsaysNMWoh8NJRb3YsowTCj2D6yhwyEdj',
      },
    ],
    authentication: ['did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411#key-1'],
    // updates did document with a service block
    service: [
      new DidDocumentService({
        id: 'did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411#rand',
        type: 'rand',
        serviceEndpoint: 'https://rand.in',
      }),
    ],
  },
})
```
