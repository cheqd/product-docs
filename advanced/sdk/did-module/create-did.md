# Create DIDs with cheqd SDK

This guide shows how to create a `did:cheqd` DID with the cheqd SDK and check that it exists. Every example below was run against the cheqd testnet with `@cheqd/sdk@5.5.1`.

## Prerequisites

1. [Set up the cheqd SDK](../setup-the-cheqd-sdk.md), which gives you `sdk` and `feePayer`.
2. A funded fee-payer wallet. Creating a DID costs about $2 in CHEQ; see [Transaction fees](README.md#transaction-fees). On testnet, request a few thousand CHEQ from the [Testnet Faucet](../../../network/tools/testnet-faucet.md).

## Two kinds of key

* The **fee payer** is the Cosmos wallet (`cheqd1…` address) that pays for the transaction.
* The **DID key** is a separate Ed25519 key pair that controls the DID. You generate it below. **Keep its private key**: it is the only way to update or deactivate the DID, and cheqd cannot recover it.

## Create a DID

```typescript
import {
  CheqdNetwork,
  MethodSpecificIdAlgo,
  VerificationMethods,
  createDidPayload,
  createDidVerificationMethod,
  createKeyPairBase64,
  createVerificationKeys,
  type ISignInputs,
} from '@cheqd/sdk';

// 1. Generate the DID's key pair
const keyPair = createKeyPairBase64();

// 2. Build the verification key, method and DID document
const verificationKeys = createVerificationKeys(
  keyPair.publicKey,
  MethodSpecificIdAlgo.Base58,
  'key-1',
  CheqdNetwork.Testnet, // must match the network of your SDK instance
);
const verificationMethods = createDidVerificationMethod([VerificationMethods.Ed255192020], [verificationKeys]);
const didPayload = createDidPayload(verificationMethods, [verificationKeys]);

// 3. Sign with the DID key
const signInputs: ISignInputs[] = [
  {
    verificationMethodId: didPayload.verificationMethod![0].id,
    privateKeyHex: Buffer.from(keyPair.privateKey, 'base64').toString('hex'),
  },
];

// 4. Submit. Argument order: signInputs, didPayload, address, fee, memo, versionId, feeOptions.
const result = await sdk.createDidDocTx(
  signInputs,
  didPayload,
  feePayer,
  undefined, // fee: omit to let the SDK price it
  undefined, // memo
  undefined, // versionId
  undefined, // feeOptions
);

if (result.code !== 0) throw new Error(`Failed to create DID: ${result.rawLog}`);
console.log(`Created ${didPayload.id} in tx ${result.transactionHash}`);
// Store keyPair.privateKey securely now.
```

{% hint style="warning" %}
**Pass every argument.** The SDK appends its own context object after the last argument you pass. Calling `sdk.createDidDocTx(signInputs, didPayload, feePayer)` with fewer arguments throws `Cannot read properties of undefined (reading 'sdk')`, and TypeScript does not warn about it. Use `undefined` for the optional arguments you do not need.
{% endhint %}

### Fees

Leave `fee` as `undefined` and the SDK prices the transaction from the network's current gas price and the USD-fixed identity fee. You do not need to calculate anything.

## Check that it worked

Read the DID back through the SDK:

```typescript
const { didDocument, didDocumentMetadata } = await sdk.queryDidDoc(didPayload.id!);
console.log(didDocument?.id, didDocumentMetadata?.versionId);
```

Or resolve it over HTTP with the cheqd DID resolver, which also works for DIDs you do not own:

```bash
curl -sL -H 'Accept: application/ld+json;profile="https://w3id.org/did-resolution"' \
  "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:<id>"
```

With that `Accept` header the response is `{ didDocument, didDocumentMetadata, … }`. With `Accept: application/did+ld+json` you get the bare DID document instead.

## More examples

### Two keys and a service endpoint

Additional keys must be attached to the **first key's DID**. Pass the first key's `didUrl` as the last argument of `createVerificationKeys` for each extra key; otherwise each key creates its own DID and the transaction fails with a "not found" error.

```typescript
const key1 = createKeyPairBase64();
const key2 = createKeyPairBase64();

const keys1 = createVerificationKeys(key1.publicKey, MethodSpecificIdAlgo.Base58, 'key-1', CheqdNetwork.Testnet);
const keys2 = createVerificationKeys(
  key2.publicKey,
  MethodSpecificIdAlgo.Base58,
  'key-2',
  CheqdNetwork.Testnet,
  undefined, // methodSpecificId
  keys1.didUrl, // attach to the same DID
);

const methods = createDidVerificationMethod(
  [VerificationMethods.Ed255192020, VerificationMethods.Ed255192020],
  [keys1, keys2],
);

const payload = {
  ...createDidPayload(methods, [keys1, keys2]),
  service: [{ id: `${keys1.didUrl}#website`, type: 'LinkedDomains', serviceEndpoint: ['https://example.com'] }],
};

// createDidPayload lists both keys in `authentication`, so both must sign.
const signInputs: ISignInputs[] = [
  { verificationMethodId: payload.verificationMethod![0].id, privateKeyHex: Buffer.from(key1.privateKey, 'base64').toString('hex') },
  { verificationMethodId: payload.verificationMethod![1].id, privateKeyHex: Buffer.from(key2.privateKey, 'base64').toString('hex') },
];

await sdk.createDidDocTx(signInputs, payload, feePayer, undefined, undefined, undefined, undefined);
```

### JsonWebKey2020

Use `VerificationMethods.JWK` in place of `VerificationMethods.Ed255192020`; everything else is the same:

```typescript
const methods = createDidVerificationMethod([VerificationMethods.JWK], [verificationKeys]);
```

### UUID-style identifier

By default the method-specific id is derived from the public key (Base58). To get a UUID instead, use `MethodSpecificIdAlgo.Uuid`:

```typescript
const verificationKeys = createVerificationKeys(keyPair.publicKey, MethodSpecificIdAlgo.Uuid, 'key-1', CheqdNetwork.Testnet);
```

## Common errors

| Error                                                 | Cause and fix                                                                                                    |
| ----------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `Cannot read properties of undefined (reading 'sdk')` | Fewer arguments than the full signature. Pass every argument.                                                    |
| `Account 'cheqd1…' does not exist on chain`           | The fee-payer wallet has never been funded. See [Testnet Faucet](../../../network/tools/testnet-faucet.md).      |
| `<did>: not found`                                    | An extra key was not attached to the first key's DID (see above).                                                |
| `insufficient fees`                                   | A hand-written `fee` is too low. Omit `fee` so the SDK prices it.                                                |
| Type error on `wallet`                                | A second `@cosmjs/*` copy is installed; see [Setup the cheqd SDK](../setup-the-cheqd-sdk.md).                    |

## Next steps

* [Manage a DID](manage-a-did.md): update and deactivate.
* [Create a DID-Linked Resource](../resource-module/create-resource.md)
