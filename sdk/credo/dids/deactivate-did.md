---
description: Deactivate a did:cheqd DID using the Credo Agent.
---

# Deactivate a DID

This guide explains how to **deactivate a `did:cheqd`** using a configured Credo Agent. Deactivating a DID marks it as no longer usable but does **not** remove it from the ledger — the DID can still be resolved and its deactivated state will be reflected in its metadata.

## Prerequisites

Before deactivating a DID, ensure that:

* The **Credo agent is configured** with cheqd support
* The DID you are deactivating was created and is controlled by your agent
* You have access to the signing key used to authorize DID updates

## Behavior of Deactivated DIDs

* Deactivated DIDs **remain resolvable**
* DID resolvers will indicate that the DID is **deactivated**
* The DID and its associated document become **immutable and non-functional**
* Deactivation is **permanent**

### Parameters

| Parameter | Required | Description                                                    |
| --------- | -------- | -------------------------------------------------------------- |
| `did`     | ✅        | The full `did:cheqd` identifier you wish to deactivate         |
| `options` | ❌        | Optional settings, including a `versionId` to track the update |

### Example: Deactivate a cheqd DID

```ts
await agent.dids.deactivate({
  did: 'did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411',
  options: {
    versionId: '3.0', // Optional: for tracking version history or audit purposes
  },
})
```

***

### Notes

* The optional `versionId` parameter allows you to assign a custom version identifier to the deactivation transaction
* Once deactivated, the DID cannot be updated, reactivated, or used for issuing credentials
* You can still resolve the DID to verify its deactivated status via the `deactivated: true` metadata
