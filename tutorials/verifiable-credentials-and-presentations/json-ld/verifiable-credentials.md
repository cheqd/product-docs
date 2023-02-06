# Issue a JSONLD Verifiable Credential

This tutorial offers step-by-step guidance on how to issue a [JSONLD credential](https://www.w3.org/TR/vc-data-model/#json-ld), encoded as a [Data Integrity Proofs](https://www.w3.org/TR/vc-data-model/#data-integrity-proofs), a [W3C compliant proof format.](https://www.w3.org/TR/vc-data-model/#proof-formats)

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/setup-cli.md) for Veramo CLI

## Background

The subject (holder) of this credential will be an off-ledger [`did:key`](https://github.com/w3c-ccg/did-method-key) DID. The rationale for using off-ledger DIDs when issuing a credential is because the fact that they _aren't_ persisted on ledger means they cannot be correlated or tracked to specific users/individuals.

This allows a user to have _multiple_ `did:key` identities, and the identifier/handle they reveal when sharing a [Verifiable Credential](verifiable-credentials.md#issue-a-json-jwt-verifiable-credential) is context-dependent and better for privacy.

The _issuer_ of the credential, on the other hand, uses [an on-ledger `did:cheqd` DID](../../did-operations/) since this needs to be publicly-accessible and cryptographically verifiable.

## Instructions

### 1. Create an off-ledger `did:key` subject DID

Normally, the credential holder will _provide_ a `did:key` DID they have generated themselves (usually done in the background by apps they are using). So, this step _typically_ isn't done by a credential issuer.

For the purpose of this tutorial though, we recommend you [create a `did:key` subject DID](../../did-operations/create-subject-did.md) for the later steps.

### 2. Begin credential creation

Start credential generation using:

```bash
veramo credential create --json
```

### 3. Select a credential proof format

Choose the option`lds` (JSON-LD).

### 4. Specify the Issuer DID for the credential

Select which Issuer DID stored in your local storage you'd like to issue the credential from, e.g., `did:cheqd:testnet:d3e515cf-81af-40cb-9ac1-154827986d29`

The assumption here is that you've either [created the issuer DID](../../did-operations/) or [stored the DIDDoc by querying it](../../did-operations/query-did.md). The Issuer DID must contain an assertionMethod inorder to issue a JSONLD credential. You can [update your DID](../../did-operations/update-did.md) by adding an assertionMethod. e.g. `"assertionMethod": [ "did:cheqd:testnet:d3e515cf-81af-40cb-9ac1-154827986d29#key-1" ]`

### 5. Specify the subject DID (credential holder's DID)

Select which subject DID from your local agent storage the credential should be issued to, e.g., `did:key:z6MkfFb5bMTvm3kXMB5zZSrLGLdtW13wU9w6ByJ76LV7U75a`.

This could the subject DID created in Step 1.

### 6. Set the Verifiable Credential type

This is currently free-text. However, you can hit _Enter_ to use `VerifiableCredential` as the default credential context.

### 7. Specify claim type, i.e., a "field" in the credential

The claim type can be thought of as the _label_ in a form field. It can denote the purpose/attribute being stored in the credential.

For example, the claim type `name` could refer to the name of an individual.

### 8. Enter value for claim type

Enter the _value_ of the claim type. For example, you specified `name` as the claim type, the value could be `Alice`.

In this tutorial, we will create a credential with only a single name-value pair.

### 9. Define if the credential is revocable or not

JSON/JWT credentials anchored on cheqd are currently _not_ revocable, so please choose `No` at this step.

[Privacy-preserving credential revocation](https://product.cheqd.io/updates/roadmap/identity) is a major element of cheqd's roadmap.

### 10. Verifiable Credential is generated

At this last step, Veramo CLI generates the credential based on the inputs above.

_Note_: The human-readable JSON body below is purely for easier parsing by developers/applications. The proof is encoded as JWT at the bottom of the credential, which can be decoded to reconstruct the entire credential.

#### Example Verifiable Credential

```json
{
  "issuer": { "id": "did:cheqd:testnet:d3e515cf-81af-40cb-9ac1-154827986d29" },
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://veramo.io/contexts/profile/v1"
  ],
  "type": [ "VerifiableCredential", "Profile" ],
  "issuanceDate": "2023-02-06T06:46:32.487Z",
  "credentialSubject": {
    "id": "did:key:z6MkfFb5bMTvm3kXMB5zZSrLGLdtW13wU9w6ByJ76LV7U75a",
    "name": "Alice"
  },
  "proof": {
    "type": "Ed25519Signature2018",
    "created": "2023-02-06T06:46:33Z",
    "verificationMethod": "did:cheqd:testnet:d3e515cf-81af-40cb-9ac1-154827986d29#key-1",
    "proofPurpose": "assertionMethod",
    "jws": "eyJhbGciOiJFZERTQSIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19..Kfq2VC0MgvcGeJ0dxFZd4RQLBD1MvKhkqSnDLeUKCNRW-PoU679FJ4cpYrIfDQK9DsegGC0AF5_ycPNfTdzUCQ"
  }
}
```

## Next steps

You can save the generated Verifiable Credential and [generate _presentations_ to show the credential](../json-jwt/verifiable-presentations.md) to others.
