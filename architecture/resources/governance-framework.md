# Governance Framework

## Overview

[ToIP recommends making Governance Frameworks available through DID URLs](https://wiki.trustoverip.org/pages/viewpage.action?pageId=71241), which would typically be a text file, a [Markdown file](https://en.wikipedia.org/wiki/Markdown), PDF etc. This, for example, can enable parties building self-sovereign identity ecosystems to use DIDs to reference Governance Frameworks they conform to, at different levels of the technical stack.

## Example of Resource for Governance Framework

cheqd could create a Resource as follows for its Governance Framework

```json
Governance Framework
{
  "header": {
    "collectionId":       "93235d54-53cc-42b9-ae77-f99efe9eadc9",
    "id":                 "8da12c4a-fa16-4895-b6c6-fc7cbdf62193",
    "name":               "cheqdGovernanceFramework",
    "resourceType":       "Text",
    "mimeType":           "text/plain"
    "created":            "2022-06-20T14:12:53Z",
    "checksum":           "a7c369ee9da8b25a2d6e93973fa8ca939b75abb6c39799d879a929ebea1adc0a",
    "previousVersionId:   "adb023ce-3b6d-48c3-aadd-134837ba62fa",
    "nextVersionId:       null
  },
  "data":                 <cheqdGovFramework.data>,
}
```

## Creating on-ledger Governance Framework

### 1. Create a Collection DID Document&#x20;

```json
{
  "id": "did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9",
  "alsoKnownAs": "https://gov.cheqd.io/"
  "verification_method": [
    {
      "id": "did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9",
      "public_key_multibase": "<verification-public-key-multibase>"
    }
  ],
  "authentication": [
    "did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9#key1"
}
```

### 2. Create Governance Framework Resource

Using the same private verification key as is created to authenticate with the DID Document, create and sign a transaction to create a resource for the Governance Framework

```
cheqd-noded tx resource create-resource "{
                                          \"collectionId\":  \"93235d54-53cc-42b9-ae77-f99efe9eadc9\",
                                          \"id\":             \"adb023ce-3b6d-48c3-aadd-134837ba62fa\",
                                          \"name\":           \"cheqdGovernanceFramework\",
                                          \"resourceType\":  \"Text\",
                                          \"mimeType\":      \"text/plain\"
                                         }" cheqdGovFramework.data\
                                          --private-key <private-identity-key-by-collection->
```

### 3. Update the Collection DID Document with appropriate service section

```json
{
  "id": "did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9",
  "alsoKnownAs": "https://gov.cheqd.io/"
  "verification_method": [
    {
      "id": "did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9",
      "public_key_multibase": "<verification-public-key-multibase>"
    }
  ],
  "authentication": [
    "did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9#key1"
  ],
  "service": [
    {
        "id": "did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9#cheqdGovernanceFramework",
        "type": "Text",
        "serviceEndpoint": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9/resources/adb023ce-3b6d-48c3-aadd-134837ba62fa"]
    }
    {
        "id": "did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9#GovernanceFrameworkWebsite",
        "type": "LinkedDomains",
        "serviceEndpoint": "https://gov.cheqd.io/"
    }
  ]
}
```

## Referencing Governance Framework resource in Issuer DID

```json
{
  "id": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c",
  "verification_method": [
    {
      "id": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c",
      "public_key_multibase": "<verification-public-key-multibase>"
    }
  ],
  "authentication": [
    "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c#key1"
  ],
  "service": [
    {
      "id": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c#Website",
      "type": "LinkedDomains",
      "serviceEndpoint": "https://www.cheqd.io"
    },
    {
      "id": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c#GovernanceFrameworkWebsite",
      "type": "LinkedDomains",
      "serviceEndpoint": "https://gov.cheqd.io/"
    },
    {
        "id": "did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9#cheqdGovernanceFramework",
        "type": "text",
        "serviceEndpoint": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:93235d54-53cc-42b9-ae77-f99efe9eadc9/resources/adb023ce-3b6d-48c3-aadd-134837ba62fa"
    }
  ]
}
```
