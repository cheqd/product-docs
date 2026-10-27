# Manage a DID

This guide shows how to update and deactivate a `did:cheqd` DID with the cheqd SDK. Every example below was run against the cheqd testnet with `@cheqd/sdk@5.5.1`.

## Prerequisites

1. [Set up the cheqd SDK](../setup-the-cheqd-sdk.md) (`import { sdk, feePayer } from './connect.js';`) and [create a DID](create-did.md).
2. The DID's **private key** (the Ed25519 key you generated when you created it) and the id of one of its verification methods, for example `did:cheqd:testnet:<id>#key-1`.
3. A funded fee-payer wallet. An update costs about $1 and a deactivation about $0.40; see [Transaction fees](README.md#transaction-fees).

## Update a DID

An update **replaces the whole DID document**. So read the current document, change it, and send the full result back.

```typescript
import { type ISignInputs } from '@cheqd/sdk';

const did = 'did:cheqd:testnet:<id>';

// 1. Read the current document. `didDocument` is null if the DID does not exist.
const current = (await sdk.queryDidDoc(did)).didDocument;
if (!current) throw new Error(`${did} not found`);

// 2. Change it: here, add a service endpoint
const updated = {
  ...current,
  service: [
    ...(current.service ?? []).filter((s) => s.id !== `${did}#website`),
    { id: `${did}#website`, type: 'LinkedDomains', serviceEndpoint: ['https://example.com'] },
  ],
};

// 3. Sign with a key that is listed in the current document
const signInputs: ISignInputs[] = [
  { verificationMethodId: `${did}#key-1`, privateKeyHex: '<hex private key of key-1>' },
];

// 4. Submit. Argument order: signInputs, didPayload, address, fee, memo, versionId, feeOptions.
const result = await sdk.updateDidDocTx(signInputs, updated, feePayer, undefined, undefined, undefined, undefined);
if (result.code !== 0) throw new Error(`Failed to update DID: ${result.rawLog}`);

// 5. Check
const after = (await sdk.queryDidDoc(did)).didDocument;
console.log(after?.service);
```

The same read, change, write pattern applies to other edits, such as adding a verification method. Each update creates a new version of the document; earlier versions remain available through `sdk.queryDidDocVersion(did, versionId)`.

{% hint style="warning" %}
**Pass every argument.** If you omit the trailing `undefined` arguments, the SDK appends its own context after your last argument and the call throws `Cannot read properties of undefined (reading 'sdk')`.

**Keep a key that can still sign.** If you rotate keys, make sure the new document still lists a key whose private key you hold, or you will not be able to update the DID again.
{% endhint %}

## Deactivate a DID

Deactivation is **permanent** and cannot be undone. The document remains resolvable but is marked deactivated.

```typescript
const current = (await sdk.queryDidDoc(did)).didDocument;
if (!current) throw new Error(`${did} not found`);

const result = await sdk.deactivateDidDocTx(signInputs, current, feePayer, undefined, undefined, undefined, undefined);
if (result.code !== 0) throw new Error(`Failed to deactivate DID: ${result.rawLog}`);

const after = await sdk.queryDidDoc(did);
console.log(after.didDocumentMetadata?.deactivated); // true
```

{% hint style="info" %}
A deactivated DID still **resolves**: the resolver returns the document with `didDocumentMetadata.deactivated: true`. Check that flag before trusting a DID.
{% endhint %}

## Common errors

| Error                                                 | Cause and fix                                                                                          |
| ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `Cannot read properties of undefined (reading 'sdk')` | Fewer arguments than the full signature. Pass every argument.                                          |
| Signature or authentication error                     | The key does not belong to a verification method in the current document, or the id does not match.    |
| `didDocument` is `null` after `queryDidDoc`           | The DID does not exist on this network. Check the network in the DID and in your SDK configuration.   |

## Next steps

* [Create a DID-Linked Resource](../resource-module/create-resource.md)
* [DID module overview](README.md)
