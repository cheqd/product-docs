# Create DID-Linked Resources with cheqd SDK

This guide shows how to create a DID-Linked Resource (DLR) with the cheqd SDK and read it back. A DLR is an immutable, versioned piece of data (a schema, a credential definition, a status list or any custom content) stored on the cheqd network under a DID.

Every example below was run against the cheqd testnet with `@cheqd/sdk@5.5.1`.

## Prerequisites

1. [Set up the cheqd SDK](../setup-the-cheqd-sdk.md) and fund a fee-payer wallet. Put `import { sdk, feePayer } from './connect.js';` at the top of your file.
2. [Create a DID](../did-module/create-did.md) to own the resource, and keep its Ed25519 private key. Resources must be signed with a key listed in the DID document.

## Create a resource

The resource payload has these fields:

| Field          | Type         | Description                                                                                                                              |
| -------------- | ------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `collectionId` | string       | The **method-specific id** of the owning DID: the last part of `did:cheqd:testnet:<id>`, **not** the full DID.                           |
| `id`           | string       | A new UUID for this resource (for example from `crypto.randomUUID()`).                                                                    |
| `name`         | string       | Name of the resource. Resources with the same `name` and `resourceType` in a collection form a version chain.                            |
| `version`      | string       | Your version label, for example `1.0.0`.                                                                                                  |
| `resourceType` | string       | For example `JSONSchema`, `CL-Schema` or `CL-CredDef`; see the [resource types](README.md#supported-resource-types) table.                |
| `data`         | `Uint8Array` | The content. The media type is detected by the network and returned in the metadata.                                                     |
| `alsoKnownAs`  | array        | Optional alternative URIs for the resource, as `{ uri, description }`.                                                                   |

```typescript
import { randomUUID } from 'node:crypto';
import {
  CheqdNetwork,
  DIDModule,
  ResourceModule,
  createCheqdSDK,
  createCosmosPayerWallet,
  type AbstractCheqdSDKModule,
  type ISignInputs,
} from '@cheqd/sdk';

const wallet = await createCosmosPayerWallet(process.env.CHEQD_MNEMONIC!);
const [{ address: feePayer }] = await wallet.getAccounts();

const sdk = await createCheqdSDK({
  modules: [
    DIDModule as unknown as AbstractCheqdSDKModule,
    ResourceModule as unknown as AbstractCheqdSDKModule,
  ],
  rpcUrl: 'https://rpc.cheqd.network', // testnet
  network: CheqdNetwork.Testnet,
  wallet,
});

// The DID that owns the resource, and the private key of one of its verification methods.
const did = 'did:cheqd:testnet:<your-did-id>';
const didPrivateKeyHex = '<hex private key of key-1>';
const collectionId = did.split(':').pop()!; // method-specific id only

const signInputs: ISignInputs[] = [
  {
    verificationMethodId: `${did}#key-1`,
    keyType: 'Ed25519', // required for resource transactions
    privateKeyHex: didPrivateKeyHex,
  },
];

const schema = {
  $schema: 'https://json-schema.org/draft/2020-12/schema',
  title: 'Person',
  type: 'object',
  properties: { firstName: { type: 'string' } },
  required: ['firstName'],
};

const resourceId = randomUUID();

// Argument order: signInputs, resourcePayload, address, fee, memo, feeOptions.
// Pass every argument: the SDK adds its own context after the last one you give.
const result = await sdk.createLinkedResourceTx(
  signInputs,
  {
    collectionId,
    id: resourceId,
    name: 'person-schema',
    version: '1.0.0',
    resourceType: 'JSONSchema',
    data: new TextEncoder().encode(JSON.stringify(schema)),
    alsoKnownAs: [{ uri: `https://example.com/schemas/${resourceId}`, description: 'Registry copy' }],
  },
  feePayer,
  undefined, // fee: omit to let the SDK price it (see below)
  undefined, // memo
  undefined, // feeOptions
);

if (result.code !== 0) throw new Error(`Failed to create resource: ${result.rawLog}`);
console.log(`Resource ${did}/resources/${resourceId} created in tx ${result.transactionHash}`);
```

{% hint style="warning" %}
Two details that cause most failures:

* **`keyType: 'Ed25519'` is required** on every sign input for resource transactions. Without it the SDK throws `Unsupported signature type: undefined`.
* **Pass all arguments.** Calling `sdk.createLinkedResourceTx(signInputs, payload, feePayer)` with fewer arguments throws `Cannot read properties of undefined (reading 'sdk')`, because the SDK appends its context object after the last argument you pass. TypeScript does not warn about this.
{% endhint %}

### Fees

Leave `fee` as `undefined` and the SDK prices the transaction from the current network gas price. Identity fees are fixed in USD (about $0.40 for a resource) and paid in CHEQ at the current rate, so the CHEQ amount varies with the CHEQ price.

## Read a resource back

```typescript
// By id
const { resource, metadata } = await sdk.queryLinkedResource(collectionId, resourceId);
console.log(metadata?.name, metadata?.version, metadata?.mediaType); // person-schema 1.0.0 application/json
console.log(JSON.parse(new TextDecoder().decode(resource?.data)));

// All resources in the collection (metadata only)
const { resources } = await sdk.queryLinkedResources(collectionId);

// The latest version of a named resource
const latest = await sdk.queryLatestLinkedResourceVersion(collectionId, 'person-schema', 'JSONSchema');
```

The metadata includes `checksum`, `created`, `mediaType`, `previousVersionId` and `nextVersionId`. The network also adds a `did-url` entry to `alsoKnownAs` that points at the resource's own DID URL.

You can also fetch a resource's content over HTTP from the DID resolver: `https://resolver.cheqd.net/1.0/identifiers/<did>/resources/<resourceId>` returns the stored content.

## Common errors

| Error                                                   | Cause and fix                                                                                                         |
| ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `Unsupported signature type: undefined`                 | Add `keyType: 'Ed25519'` to the sign input.                                                                           |
| `Cannot read properties of undefined (reading 'sdk')`   | Pass every argument of `createLinkedResourceTx`, using `undefined` for the optional ones.                             |
| `Account 'cheqd1…' does not exist on chain`             | The fee-payer wallet has never been funded. See [Testnet Faucet](../../../network/tools/testnet-faucet.md).            |
| Signature or authentication failure                     | The key must belong to the DID in `collectionId`, and `verificationMethodId` must match a method in its document.      |

## Next steps

* [Manage a DID-Linked Resource](manage-resource.md): create new versions and navigate the version chain.
* [Resource module overview](README.md)
