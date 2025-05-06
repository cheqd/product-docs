---
description: Update a did:cheqd Decentralized Identifier (DID) using cheqd Studio.
---

# Update a DID

Users are able to update DID Documents for various reasons, such as to include a new section (such as `service`) or to rotate the `verificationMethod` or `authentication` keys within the DID Document.&#x20;

With the `/did/update` API, users are able to input either:

1. &#x20;The specific section(s) of the DID Document to be updated; or
2. The full updated DID Document body.

{% hint style="info" %}
For the `/did/update` function, users are recommended to use the `application/json` request format for the API.
{% endhint %}

## Example Request formats

#### Update Service Section

To update individual sections of the DID Document, users are able to pass only the section they would like to update. For example, users may often want to update the `service` section to add or change a `serviceEndpoint`:

```json
{
  "did": "did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881",
  "service": [
      {
        "id": "did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881#website",
        "type": "LinkedDomains",
        "serviceEndpoint": [
          "https:example.com"
        ]
      }
    ]
}
```

The above request format will replace the `service` section of the existing DID Document with the new section. No extra information, such as the verificationMethod or `authentication` keys need to be passed with the request, because cheqd Studio custodies the keys on behalf of the user.&#x20;

#### Rotate Keys

Users may regularly want to rotate the keys used to control the DID. Like with the `service` section above, users are able to pass the sections of the DID Document that include the rotated keys, usually contained within the`verificationMethod` and `authentication` sections.

{% hint style="info" %}
Other sections such as `keyAgreement`, `capabilityInvocation`, `capabilityDelegation`, `assertionMethod`, etc. may also be updated via this method
{% endhint %}

```json
{
  "did": "did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881",
  "verificationMethod": [
     {
       "id":"did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881",
       "type":"Ed25519VerificationKey2018",
       "controller":"did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881",
       "publicKeyBase58":"4sjwZ3VGWupJ26ytxjhU6LhjKiPrHV5reZbaNseyHtf8"
     }
  ],
  "authentication": [
       "did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881#key-1"
  ]
}
```

The example above will rotate the keys that control the DID Document, updating the DID Document's `verificationMethod` and `authentication` sections accordingly.&#x20;

{% hint style="info" %}
Any top level section of the DID Document may be updated this way, including the likes of `alsoKnownAs`, `assertionMethod`, `capabilityDelegation`, etc.&#x20;
{% endhint %}

### Pass full updated DID Document body

Users may alternatively want to update the DID Document body in full, and pass the updated DID Document to the API. For example:

```json
{
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1",
      "https://w3id.org/security/suites/ed25519-2018/v1"
    ],
    "id": "did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881",
    "controller": [
      "did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881"
    ],
    "verificationMethod": [
      {
        "id": "did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881#key-1",
        "type": "Ed25519VerificationKey2018",
        "controller": "did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881",
        "publicKeyBase58": "4sjwZ3VGWupJ26ytxjhU6LhjKiPrHV5reZbaNseyHtf8"
      }
    ],
    "authentication": [
      "did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881#key-1"
    ],
    "service": [
      {
        "id": "did:cheqd:mainnet:71e24492-e62c-4e55-a581-007a6e36e881#website",
        "type": "LinkedDomains",
        "serviceEndpoint": [
          "https://example.com"
        ]
      }
    ]
  }
}
```

This method may be better for users wanting to update larger portions of the DID Document, or for having full visibility over the updated body.

## Get started with the API below

Use the `/did/update` API below to pass your DID Document update as a request, using either the full body or a specific section.

{% openapi-operation spec="cheqd-studio-api" path="/did/update" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}
