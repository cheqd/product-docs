# Status List v2021

The [Verifiable Credential Status List v2021 Specification](https://w3c-ccg.github.io/vc-status-list-2021/#conceptual-framework) is a working document from the W3C to support a privacy-preserving, space-efficient, and high-performance mechanism for publishing status information such as suspension or revocation of JSON and JSON-LD Verifiable Credentials.

## Understanding Status List v2021

The [Status List 2021 Specification](https://w3c-ccg.github.io/vc-status-list-2021/) utilises [bitstrings](https://w3c-ccg.github.io/vc-status-list-2021/#conceptual-framework) to represent whether a Verifiable Credential has been suspended/revoked or not. A bitstring can be thought of as a long list of 1s and 0s, where, if the binary value of the position in the list is 1 (one), the [verifiable credential](https://w3c-ccg.github.io/vc-status-list-2021/#dfn-verifiable-credentials) is revoked, if it is 0 (zero) it is not revoked.

![Graphic showing the StatusList2021 bitstring](<../../../.gitbook/assets/StatusList21 Bitstring.png>)

_**Figure 1**: Graphic showing the StatusList2021 bitstring_

Each issued Credential correlates with a position and index on the bitstring, so that a verifier will be able to correlate the value within the Credential against the public bitstring to ascertain whether the Credential has been revoked or not, using a [validate algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#validate-algorithm) as well as a [bitstring expansion algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#bitstring-expansion-algorithm).

### Where is the StatusList usually published?

The issuer keeps a bitstring list of all Verifiable Credentials it has issued. The StatusList is published by the issuer **in the format of its own Verifiable Credential.** This Verifiable Credential is generally hosted publicly on a centralised server or domain to enable third-party read-access.

The following JSON syntax shows the common format for creating a StatusList2021 definition, using a Verifiable Credential.

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3id.org/vc/status-list/2021/v1"
  ],
  "id": "https://example.com/credentials/status/3",
  "type": ["VerifiableCredential", "StatusList2021Credential"],
  "issuer": "did:example:12345",
  "issued": "2021-04-05T14:27:40Z",
  "credentialSubject": {
    "id": "https://example.com/status/3#list",
    "type": "StatusList2021",
    "statusPurpose": "revocation",
    "encodedList": "H4sIAAAAAAAAA-3BMQEAAADTCoPVPbQwfoAAAAAAAAAAAAAAAAAAAAIC3AYbSVKsAQAAA"
  },
  "proof": { ... }
}
```

The bitstring here is stored in the `encodedList` property. This value _MUST_ be a GZIP-compressed base-64 encoded bitstring value for the associated range of Verifiable Credential status values.

{% hint style="info" %}
Note: The uncompressed bitstring _MUST_ be at least 16KB in size to maintain herd privacy for the holder.
{% endhint %}

More specifically, each value has the following definition and use:

| Property                          | Description                                                                                                                                                                                                                                                                                        |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`                              | The verifiable credential that contains the status list _MAY_ express an `id` property that matches the value specified in `statusListCredential` for the corresponding `StatusList2021Entry` (see [2.1 StatusList2021Entry](https://w3c-ccg.github.io/vc-status-list-2021/#statuslist2021entry)). |
| `type`                            | The verifiable credential that contains the status list _MUST_ express a `type` property that includes the `StatusList2021Credential` value.                                                                                                                                                       |
| `credentialSubject.type`          | The `type` of the credential subject, which is the status list, _MUST_ be `StatusList2021`.                                                                                                                                                                                                        |
| `credentialSubject.statusPurpose` | The purpose of the status entry _MUST_ be a string. While the value of the string is arbitrary, the following values _MUST_ be used for their intended purpose: **revocation** or **suspension.**                                                                                                  |
| `credentialSubject.encodedList`   | The `encodedList` property of the credential _MUST_ be the GZIP-compressed, base-64 encoded bitstring values for the associated range of verifiable credential status values. The uncompressed bitstring _MUST_ be at least 16KB in size.                                                          |

When an issuer wants to issue a Verifiable Credential relying on StatusList2021, they must include the following `credentialStatus` properties within the Verifiable Credential itself.

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3id.org/vc/status-list/2021/v1"
  ],
  "id": "https://example.com/credentials/23894672394",
  "type": ["VerifiableCredential"],
  "issuer": "did:example:12345",
  "issued": "2021-04-05T14:27:42Z",
  "credentialStatus": {
    "id": "https://example.com/credentials/status/3#94567"
    "type": "StatusList2021Entry",
    "statusPurpose": "revocation",
    "statusListIndex": "94567",
    "statusListCredential": "https://example.com/credentials/status/3"
  },
  "credentialSubject": {
    "id": "did:example:6789",
    "type": "Person"
  },
  "proof": { ... }
}
```

Where each property has the following meaning:

| Property               | Description                                                                                                                                                                                                                                                                                                                                                      |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`                   | The constraints on the `id` property are listed in the Verifiable Credentials Data Model specification \[[_VC-DATA-MODEL_](https://w3c-ccg.github.io/vc-status-list-2021/#bib-vc-data-model)]. The value is expected to be a URL that identifies the status information associated with the verifiable credential. It _MUST NOT_ be the URL for the status list. |
| type                   | The `type` property _MUST_ be `StatusList2021Entry`                                                                                                                                                                                                                                                                                                              |
| `statusPurpose`        | The purpose of the status entry _MUST_ be a string. While the value of the string is arbitrary, the following values _MUST_ be used for their intended purpose: **revocation** or **suspension**                                                                                                                                                                 |
| `statusListIndex`      | The `statusListIndex` property _MUST_ be an arbitrary size integer greater than or equal to 0, expressed as a string. The value identifies the bit position of the status of the verifiable credential.                                                                                                                                                          |
| `statusListCredential` | The `statusListCredential` property _MUST_ be a URL to a verifiable credential. When the URL is dereferenced, the resulting verifiable credential _MUST_ have `type` property that includes the `StatusList2021Credential` value.                                                                                                                                |

As such, the issued Verifiable Credential references the centralised parent StatusList Credential controlled and stored by the issuer.

