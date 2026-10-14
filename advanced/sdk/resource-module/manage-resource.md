# Manage DID-Linked Resources with cheqd SDK

DID-Linked Resources are **immutable**: once written, a resource cannot be changed or deleted. To "update" a resource you publish a **new version** of it. This guide shows how versions are linked and how to find the latest one.

Every example below was run against the cheqd testnet with `@cheqd/sdk@5.5.1`.

## How versioning works

* A **version chain** is made of resources in the same collection (the same DID) that share a `name` and a `resourceType`.
* Each new resource you create with the same `name` and `resourceType` is appended to the chain. The network sets `previousVersionId` on the new one and `nextVersionId` on the one before it.
* The `version` field is a label you choose, such as `1.1.0`. The chain order is the order of creation, not the label.
* Different `name` or `resourceType` starts a separate chain.

## Prerequisites

1. [Set up the cheqd SDK](../setup-the-cheqd-sdk.md) and have a funded fee-payer wallet.
2. A DID and its Ed25519 private key, and the setup used in [Create a DID-Linked Resource](create-resource.md) (`sdk`, `feePayer`, `collectionId`, `signInputs` with `keyType: 'Ed25519'`).

## Publish a new version

Use the same `name` and `resourceType` as the existing resource, a new `id` and a new `version` label:

```typescript
import { randomUUID } from 'node:crypto';

async function publishVersion(version: string, content: object) {
  const id = randomUUID();
  const result = await sdk.createLinkedResourceTx(
    signInputs,
    {
      collectionId,
      id,
      name: 'person-schema', // same as before
      version,
      resourceType: 'JSONSchema', // same as before
      data: new TextEncoder().encode(JSON.stringify(content)),
    },
    feePayer,
    undefined, // fee
    undefined, // memo
    undefined, // feeOptions
  );
  if (result.code !== 0) throw new Error(`Failed to create version: ${result.rawLog}`);
  return id;
}

const v1 = await publishVersion('1.0.0', { title: 'Person' });
const v2 = await publishVersion('1.1.0', { title: 'Person v2' });
```

{% hint style="info" %}
As with every `sdk.*Tx` method, pass all arguments (using `undefined` for optional ones) and set `keyType: 'Ed25519'` on the sign inputs. See [Create a DID-Linked Resource](create-resource.md).
{% endhint %}

## Find the latest version

```typescript
const latest = await sdk.queryLatestLinkedResourceVersion(collectionId, 'person-schema', 'JSONSchema');
console.log(latest.metadata?.id, latest.metadata?.version); // the id of v2, "1.1.0"

// Metadata only, without downloading the content
const meta = await sdk.queryLatestLinkedResourceVersionMetadata(collectionId, 'person-schema', 'JSONSchema');
```

## Walk the version chain

Each resource's metadata links to its neighbours:

```typescript
const first = await sdk.queryLinkedResource(collectionId, v1);
console.log(first.metadata?.previousVersionId); // "" (empty): this is the first version
console.log(first.metadata?.nextVersionId); // the id of v2

const second = await sdk.queryLinkedResource(collectionId, first.metadata!.nextVersionId);
```

List everything in the collection with `sdk.queryLinkedResources(collectionId)`, which returns `{ resources, pagination }` with the metadata of each resource.

## Best practice

* Use a clear `name` and a consistent `resourceType` so consumers can ask for "the latest `person-schema`".
* Link to a specific `id` (for example `did:cheqd:testnet:<id>/resources/<resourceId>`) when you need a credential to keep referring to exactly one version.
* Because nothing can be deleted, do not store personal data in a resource.

## Common errors

See [Common errors](create-resource.md#common-errors) on the create page: `Unsupported signature type: undefined`, `Cannot read properties of undefined (reading 'sdk')` and `Account … does not exist on chain` also apply here.

## Next steps

* [Create a DID-Linked Resource](create-resource.md)
* [Resource module overview](README.md)
