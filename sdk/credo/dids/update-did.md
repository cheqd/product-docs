---
description: Update a did:cheqd DID using the Credo Agent.
---

# Update a DID

This guide walks you through how to **update an existing cheqd DID** using your configured **Credo Agent**. Updating a DID Document lets you add or modify keys, service endpoints, or other metadata associated with the DID.

## Prerequisites

Before you begin, make sure:

* ✅ Your Credo agent is already configured with cheqd support
* ✅ You have write access to the DID (i.e., the key used for DID creation or a controller key)
* ✅ You know the **DID you want to update** and its current document state

## How DID Updates Work

To update a `did:cheqd`, you must:

1. **Fetch the current DID Document**
2. **Modify** the relevant fields (e.g., add keys, update service endpoints)
3. **Submit the updated DID Document** using `agent.dids.update(...)`

### Example: Add a New Service and Verification Method

```ts
import { DidDocumentService } from '@credo-ts/core'

await agent.dids.update({
  did: 'did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411',

  // Used to authorize and derive additional keys, if needed
  secret: {
    verificationMethod: {
      id: 'key-2',
      type: 'JsonWebKey2020', // Can also be Ed25519VerificationKey2020, etc.
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

    authentication: [
      'did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411#key-1',
    ],

    service: [
      new DidDocumentService({
        id: 'did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411#website',
        type: 'linkedDomains',
        serviceEndpoint: 'https://rand.in',
      }),
    ],
  },
})
```

***

### Parameters Reference

| Parameter     | Required | Description                                                                |
| ------------- | -------- | -------------------------------------------------------------------------- |
| `did`         | ✅        | The DID you want to update                                                 |
| `didDocument` | ✅        | The **complete** updated DID Document                                      |
| `secret`      | ⚠️       | Required only if you're referencing new keys not yet in the agent’s wallet |
| `options`     | ❌        | Optional advanced settings (e.g., method-specific configurations)          |
