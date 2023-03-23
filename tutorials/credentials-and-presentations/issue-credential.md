# Issue a Verifiable Credential

This tutorial offers step-by-step guidance on how to issue a verifiable credential

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/sdk/veramo-sdk-for-cheqd/setup.md) for Veramo CLI

## Background

The subject (holder) of this credential will be an off-ledger [`did:key`](https://github.com/w3c-ccg/did-method-key) DID. The rationale for using off-ledger DIDs when issuing a credential is because the fact that they _aren't_ persisted on ledger means they cannot be correlated or tracked to specific users/individuals.

This allows a user to have _multiple_ `did:key` identities, and the identifier/handle they reveal when sharing a [Verifiable Credential](issue-credential.md) is context-dependent and better for privacy.

The _issuer_ of the credential, on the other hand, uses [an on-ledger `did:cheqd` DID](../../architecture/adr-list/adr-001-cheqd-did-method.md) since this needs to be publicly-accessible and cryptographically verifiable.

## Instructions

### 1. Create an off-ledger `did:key` subject DID

Normally, the credential holder will _provide_ a `did:key` DID they have generated themselves (usually done in the background by apps they are using). So, this step _typically_ isn't done by a credential issuer.

For the purpose of this tutorial though, we recommend you [create a `did:key` subject DID](../did-operations/create-subject-did.md) for the later steps.

### 2. Begin credential creation

Start credential generation using:

```bash
veramo credential create --json
```

### 3. Select a credential proof format

You'll be presented with an multiple options select:

* `jwt` for [JSON credential](https://www.w3.org/TR/vc-data-model/#json), encoded as a [JSON Web Token (JWT)](https://www.w3.org/TR/vc-data-model/#json-web-token), a [W3C compliant proof format.](https://www.w3.org/TR/vc-data-model/#proof-formats)
* `lds` for [JSON-LD credential](https://www.w3.org/TR/vc-data-model/#json-ld), encoded as a [Data Integrity Proofs](https://www.w3.org/TR/vc-data-model/#data-integrity-proofs), a [W3C compliant proof format.](https://www.w3.org/TR/vc-data-model/#proof-formats)

> :books: **Learn about different types of Verifiable Credentials**
>
> If you want to learn more about different types of verifiable credentials [please go over to our learning site here.](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials)

### 4. Specify the Issuer DID for the credential

Select which Issuer DID stored in your local storage you'd like to issue the credential from, e.g., `did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX`

The assumption here is that you've either [created the issuer DID](../did-operations/create-a-did.md) or [stored the DIDDoc by querying it](../did-operations/query-did.md).

### 5. Specify the subject DID (credential holder's DID)

Select which subject DID from your local agent storage the credential should be issued to, e.g., `did:key:z6Mkktr27VZ7TTFoTsD9p79JwtGnQDgJWKGrxJ79quE7M5Yx`.

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

JSON/JWT credentials with DIDs anchored on cheqd are currently _not_ revocable, so please choose `No` at this step.

[Privacy-preserving credential revocation](https://sharing.clickup.com/6600954/tl/h/69e7u-8222/6f126b298e224c8) is a major element of cheqd's roadmap.

### 10. Verifiable Credential is generated

At this last step, Veramo CLI generates the credential based on the inputs above.

_Note_: The human-readable JSON body below is purely for easier parsing by developers/applications. The proof is encoded as JWT or JWS at the bottom of the credential, which can be decoded to reconstruct the entire credential.

<details>

<summary>Example JSON Verifiable Credential</summary>

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://veramo.io/contexts/profile/v1"
  ],
  "type": ["VerifiableCredential", "Profile"],
  "issuer": {
    "id": "did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX"
  },
  "issuanceDate": "2022-07-28T15:25:10.000Z",
  "credentialSubject": {
    "name": "Alice",
    "id": "did:key:z6Mkktr27VZ7TTFoTsD9p79JwtGnQDgJWKGrxJ79quE7M5Yx"
  }, 
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vdmVyYW1vLmlvL2NvbnRleHRzL3Byb2ZpbGUvdjByb2ZpbGUvdjEiXSwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIlByb2ZpbGUiXSwiY3JlZGVudGlhbFN1YmplY3QiOnsibmFtZSI6IkFsaWNlIn19LCJzdWIiOiJkaWQ6a2V5Ono2TWtrdHIyN1ZaN1RURm9Uc25RRGdKV0tHc0Q5cDc5Snd0R25RRGdKV0tHcnhKNzlxdUU3TTVZeCIsIm5iZiI6MTY1OTAyMTkxMCwiaXNzIjoiZGlkOmNoZXFkOm1haW5uZXQ6ekFYd3dxWnpoQ1pBMUw3N1pCYThmaFZOakw5TVFDSFgifQ.MRqlKuFQzpjLvsW3C2ZSBEf5jfvJCPQBwl-gP1P8bRfNSvjxj9H3eDgDmEf5jfvJCPQBwUDltBr-ZQ3Q7SKVSvCaJHV8TnUzBA"
  }
}
```

</details>

<details>

<summary>Example JSON-LD Verifiable Credential</summary>

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://veramo.io/contexts/profile/v1"
  ],
  "type": [
    "VerifiableCredential",
    "Profile"
  ],
  "issuer": {
    "id": "did:cheqd:testnet:d3e515cf-81af-40cb-9ac1-154827986d29"
  },
  "issuanceDate": "2023-02-07T06:28:33.760Z",
  "credentialSubject": {
    "id": "did:key:z6MkfFb5bMTvm3kXMB5zZSrLGLdtW13wU9w6ByJ76LV7U75a",
    "name": "Alice"
  },
  "proof": {
    "type": "Ed25519Signature2018",
    "created": "2023-02-07T06:28:34Z",
    "verificationMethod": "did:cheqd:testnet:d3e515cf-81af-40cb-9ac1-154827986d29#key-1",
    "proofPurpose": "assertionMethod",
    "jws": "eyJhbGciOiJFZERTQSIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19..LkXa1AxEtoY78-6NwAiIe0viO271RzCMbfJsFgm7muBCHGKGMgTRO0QnGDvA0fygKAVCjvR1xZpgJQEuxlbNAg"
  }
}
```

</details>

> Note that the JSON is unordered so the output of the fields could be in a different order

## Next steps

You can save the generated Verifiable Credential, as you can later use it to [verify the credential cryptographically](verify-jwt-credential.md) or [generate _presentations_ to show the credential](create-jwt-presentation.md) to others.
