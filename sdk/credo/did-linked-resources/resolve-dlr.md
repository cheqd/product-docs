---
description: Resolve a DID-Linked Resource (DLR) on cheqd using the Credo Agent.
---

# Resolve DID-Linked Resource

The `resolveResource` method in the **Credo cheqd module** enables you to retrieve **DID-Linked Resources** stored on the cheqd network. These resources can be resolved in two primary ways:

## Resolution Methods

### 1. Resolve by Resource ID (UUID)

Use the full DID + `/resources/<resourceId>` format to directly resolve a known resource.

```ts
await agent.modules.cheqd.resolveResource(
  'did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d/resources/6de33634-6439-4e46-aa3f-bfe03606b000'
)
```

This approach is ideal when you know the exact UUID of the resource.

***

### 2. Resolve by Resource Name and Type

You can also resolve a resource using a DID URL query format with `resourceName` and `resourceType` parameters:

```ts
await agent.modules.cheqd.resolveResource(
  'did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d?resourceName=exampleTrustRegistry&resourceType=TrustRegistry'
)
```

This method is useful when:

* You want to resolve the latest version of a known logical resource
* You don’t have or track the UUID

### 3. Fetch the Resource Version Closest to a Specific Time

```ts
await agent.modules.cheqd.resolveResource(
  'did:cheqd:testnet:<uuid>?resourceName=example&resourceType=exampleType&resourceVersionTime=2023-01-01T00:00:00Z'
)
```

***

### 4. Alternative parameters

For a comprehensive list and detailed explanations of these parameters, refer to [ADR-005](https://docs.cheqd.io/product/architecture/adr-list/adr-005-did-resolution-and-did-url-dereferencing).
