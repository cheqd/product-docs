# Issue JSON based JWT Verifiable Credential

## Overview

This tutorial offers step-by-step guidance on how to issue a [JSON credential](https://www.w3.org/TR/vc-data-model/#json), encoded as a [JWT (JSON Web Token)](https://www.w3.org/TR/vc-data-model/#json-web-token), a [W3C compliant proof format.](https://www.w3.org/TR/vc-data-model/#proof-formats)

## Pre-requisites

> The subject (holder) of this credential will be a [`did:key`](https://github.com/w3c-ccg/did-method-key) DID.

If you have not yet created a [`did:key`](https://github.com/w3c-ccg/did-method-key) DID for the subject, see `create-subject-did` tutorial.  

The issuer of the DID will also be required, retrieved from your local agent.

Issuing a Verifiable Credential can be achieved through both the Veramo CLI, or directly through an application, setup to read and write to the cheqd ledger, using the Veramo SDK for cheqd.

> Ensure you have saved the [`agent.yml`](https://raw.githubusercontent.com/cheqd/did-provider-cheqd/main/agent.yml) file in your local project directory.

For full information on the architecture, setup and cofig, check [`did-provider-cheqd`]().

## Steps

### Step 1

Begin credential creation using:

```bash
veramo credential create --json
```

### Step 2

Select which credential proof format you would like to use.

You will have the option to select `jwt` or `lds`.

Select `jwt`.

> Currently `jwt` is the only proof format enabled with the Veramo SDK for cheqd.

### Step 3

Specify issuer DID.

Select which Issuer DID you would like to use from those available in your local agent.

For example:

`did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX`

### Step 4

Specify subject DID.

Select which subject DID you would like to use from those available in your local agent.

For example:

`did:key:z6Mkktr27VZ7TTFoTsD9p79JwtGnQDgJWKGrxJ79quE7M5Yx`

### Step 5

Specify your Verifiable Credential Type.

> This is currently free-text however you can hit enter to use `VerifiableCredential` as the default credential name.

### Step 6

Specify claim type (name)

Enter the purpose of the claim. This refers to the type of claim (e.g. name)

### Step 7

Claim value refers to the name of the credential owner (e.g. `Alice`). Using the CLI in it’s current state you will be able to create one claim.

### Step 8

Select credential revocability

> Currently revocation is not supported by cheqd.

Select `No`

### Step 9

Your credential will now be auto-generated.

The output should appear as follows:

```jsonc
{
  "credentialSubject": {
    "name": "Alice",
    "id": "did:key:z6Mkktr27VZ7TTFoTsD9p79JwtGnQDgJWKGrxJ79quE7M5Yx"
  },
  "issuer": {
    "id": "did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX"
  },
  "type": [
    "VerifiableCredential",
    "Profile"
  ],
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://veramo.io/contexts/profile/v1"
  ],
  "issuanceDate": "2022-07-28T15:25:10.000Z",
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vdmVyYW1vLmlvL2NvbnRleHRzL3Byb2ZpbGUvdjByb2ZpbGUvdjEiXSwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIlByb2ZpbGUiXSwiY3JlZGVudGlhbFN1YmplY3QiOnsibmFtZSI6IkFsaWNlIn19LCJzdWIiOiJkaWQ6a2V5Ono2TWtrdHIyN1ZaN1RURm9Uc25RRGdKV0tHc0Q5cDc5Snd0R25RRGdKV0tHcnhKNzlxdUU3TTVZeCIsIm5iZiI6MTY1OTAyMTkxMCwiaXNzIjoiZGlkOmNoZXFkOm1haW5uZXQ6ekFYd3dxWnpoQ1pBMUw3N1pCYThmaFZOakw5TVFDSFgifQ.MRqlKuFQzpjLvsW3C2ZSBEf5jfvJCPQBwl-gP1P8bRfNSvjxj9H3eDgDmEf5jfvJCPQBwUDltBr-ZQ3Q7SKVSvCaJHV8TnUzBA"
  }
}
```

Now that your Verifiable Credential has been generated, save the JSON file in your local storage under the root path of your project.
