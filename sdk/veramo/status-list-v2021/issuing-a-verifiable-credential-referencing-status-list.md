---
description: >-
  Issue a Verifiable Credential referencing a Bitstring Status List within the
  credentialStatus section of the Credential Body.
---

# Issuing a Verifiable Credential referencing Status List

### Issuing a Verifiable Credential referencing cheqd StatusList

Follow the [instructions for issuing a Verifiable Credential here](../credentials-and-presentations/), including the `credentialStatus` information, as shown in the example below:

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://veramo.io/contexts/profile/v1"
  ],
  "issuer": {
    "id": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY"
  },
  "type": [
    "VerifiableCredential",
    "Profile"
  ],
  "issuanceDate": "2022-07-28T15:25:10.000Z",
  "credentialSubject": {
    "name": "Alice",
    "id": "did:key:z6Mkktr27VZ7TTFoTsD9p79JwtGnQDgJWKGrxJ79quE7M5Yx"
  },
  "credentialStatus": {
    "id": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY/resources/4a71319b-00b1-4db9-bc05-56dc426f7062#94567"
    "type": "StatusList2021Entry",
    "statusPurpose": "StatusList2021Revocation",
    "statusListIndex": "94567",
    "statusListResource": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY/resources/4a71319b-00b1-4db9-bc05-56dc426f7062"
  },
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vdmVyYW1vLmlvL2NvbnRleHRzL3Byb2ZpbGUvdjByb2ZpbGUvdjEiXSwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIlByb2ZpbGUiXSwiY3JlZGVudGlhbFN1YmplY3QiOnsibmFtZSI6IkFsaWNlIn19LCJzdWIiOiJkaWQ6a2V5Ono2TWtrdHIyN1ZaN1RURm9Uc25RRGdKV0tHc0Q5cDc5Snd0R25RRGdKV0tHcnhKNzlxdUU3TTVZeCIsIm5iZiI6MTY1OTAyMTkxMCwiaXNzIjoiZGlkOmNoZXFkOm1haW5uZXQ6ekFYd3dxWnpoQ1pBMUw3N1pCYThmaFZOakw5TVFDSFgifQ.MRqlKuFQzpjLvsW3C2ZSBEf5jfvJCPQBwl-gP1P8bRfNSvjxj9H3eDgDmEf5jfvJCPQBwUDltBr-ZQ3Q7SKVSvCaJHV8TnUzBA"
  }
}
```

Where the values within the credentialSubject field are as follows:

| Property               | Description                                                                                                                                                                                                            |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`                   | A specific DID URL dereferencing to the actual status of the Credential                                                                                                                                                |
| type                   | The `type` property _MUST_ be `StatusList2021Entry`                                                                                                                                                                    |
| `statusPurpose`        | The purpose of the status entry _MUST_ be a string. While the value of the string is arbitrary, the following values _MUST_ be used for their intended purpose: **revocation** or **suspension**                       |
| `statusListIndex`      | The `statusListIndex` property _MUST_ be an arbitrary size integer greater than or equal to 0, expressed as a string. The value identifies the bit position of the status of the verifiable credential.                |
| `statusListCredential` | The `statusListCredential` property _MUST_ is a DID URL to a statusList Resource. When the URL is dereferenced, the resulting Resource _MUST_ have `type` property that includes the `StatusList2021Credential` value. |

#### Validate Algorithm for cheqd StatusList

The following process, or one generating the exact output, _MUST_ be followed when validating a verifiable credential that is contained in a cheqd StatusList Resource.

1. Let **credentialToValidate** be a verifiable credentials containing a `credentialStatus` entry that is a StatusList2021Entry, associated with an entry in a bitstring.
2. Let **status purpose** be the value of `statusPurpose` in the `credentialStatus` entry in the **credentialToValidate**.
3. Verify all proofs associated with the **credentialToValidate**. If a proof fails, return a validation error.
4. Verify that the **status purpose** matches the `resourceType` value in the **StatusList2021 Resource**.
5. Let **compressed bitstring** be the value of the `encodedList` property of the **StatusList2021 Resource**.
6. Let **credentialIndex** be the value of the `statusListIndex` property of the bitstring in the **StatusList2021 Resource**.
7. Generate a **revocation bitstring** by passing **compressed bitstring** to the [Bitstring Expansion Algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#bitstring-expansion-algorithm).
8. Let **status** be the value of the bit at position **credentialIndex** in the **revocation bitstring**.
9. Return `true` if **status** is 1, `false` otherwise.
