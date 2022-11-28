# Context for developing DID-Linked Resources

## Understanding "Resources" in decentralised identity

In self-sovereign identity (SSI) ecosystems, **“resources”** are often required in tandem with [W3C Verifiable Credentials](https://www.w3.org/TR/vc-data-model/), to provide supporting information or additional context to verifiers receiving [Verifiable Presentations](https://w3c-ccg.github.io/vp-request-spec/).

For example, common types of resources that might be required to issue and validate Verifiable Credentials are:

* Schemas;
* Status lists;
* Trust registries;
* Visual Representations of Verifiable Credentials;
* Documents; or
* Logos

### Schemas

Schemas describe [the fields and content types in a credential](https://w3c.github.io/vc-data-model/#data-schemas) in a machine-readable format. Prominent examples of this include [schema.org](https://schema.org/docs/schemas.html), [Hyperledger Indy schema objects](https://hyperledger-indy.readthedocs.io/projects/node/en/latest/transactions.html#schema), etc. You can think of them as a template for what is included in a Verifiable Credential.

Below is an example of a [schema.org](https://schema.org/docs/schemas.html) residential address with full URLs:

```json
{ 
    "@type": "http://schema.org/Person", 
    "http://schema.org/address": {   
        "@type": "http://schema.org/PostalAddress",   
        "http://schema.org/streetAddress": "123 Main St.",   
        "http://schema.org/addressLocality": "Blacksburg",   
        "http://schema.org/addressRegion": "VA",   
        "http://schema.org/postalCode": "24060",   
        "http://schema.org/addressCountry": "US" 
    }
}
```

This might also take the form of **evidence schemes**, which describe additional information about the processes used to validate the information presented in a Verifiable Credential in common, machine-readable format.

Prominent examples of this include [Schema.org](https://schema.org/docs/schemas.html), [Hyperledger AnonCreds `SCHEMA` objects](https://hyperledger.github.io/anoncreds-spec/#schema-publisher-publish-schema-object), etc.

### Status and Revocation Lists

Status lists allow recipients of a Verifiable Credential exchange to [check the status of a credential](https://w3c.github.io/vc-data-model/#validity-checks) for validity. Prominent examples of this include the [W3C `Status List 2021`](https://w3c-ccg.github.io/vc-status-list-2021/) specification, [Hyperledger AnonCreds Revocation](https://hyperledger.github.io/anoncreds-spec/#issuer-create-and-publish-revocation-registry-objects), etc.

### **Trust registries**

Trust registries enable recipients of a Verifiable Credential exchange to check that the Decentralized Identifier of the issuer is listed in a trusted registry. This provides a level of assurance in the authenticity of the issuer. Examples of Trust Registries include the [ToIP Trust Registry Specification](https://wiki.trustoverip.org/display/HOME/ToIP+Trust+Registry+Protocol+Specification), EBSI Trust Registry API, etc.

### **Visual representations for Verifiable Credentials**

Although Verifiable Credentials can be exchanged digitally, in practice most identity wallets want to present “human-friendly” representations.  Examples of this include the [Overlays Capture Architecture (OCA) specification](https://oca.colossi.network/), [Apple Wallet PassKit](https://developer.apple.com/documentation/walletpasses) ("`.pkpass`"), [Google Wallet Pass](https://developers.google.com/wallet/generic), etc. A resource, using something like [Overlay Capture Architecture (OCA)](https://humancolossus.foundation/blog/cjzegoi58xgpfzwxyrqlroy48dihwz) may enable a credential representation to be shown according to the brand guidelines of the issuer, [internationalisation (“i18n”) translations](https://en.wikipedia.org/wiki/Internationalization\_and\_localization), etc.&#x20;

<figure><img src="../../.gitbook/assets/Overlay Capture Architecture diagram.png" alt="Image of Overlay Capture Architecture"><figcaption><p>Source: <a href="https://oca.colossi.network/v1.0.0.html">Overlays Capture Architecture Specification Version 1.0.0</a></p></figcaption></figure>

_Figure 1:_ [_Overlays Capture Architecture Specification Version 1.0.0_](https://oca.colossi.network/v1.0.0.html)__

Such visual representations can also be used to quickly communicate information visually during identity exchanges, such as airline mobile boarding passes.

<figure><img src="../../.gitbook/assets/On-ledger resources - boarding passes.jpeg" alt="Image of British airways tickets in digital visual format"><figcaption></figcaption></figure>

_Figure 2: Mobile boarding passes in Apple Wallet showing different visual styles (source:_ [_British Airways media centre_](https://mediacentre.britishairways.com/pressrelease/details/86/2016-72/6130)_)_

In the [example above from British Airways](https://mediacentre.britishairways.com/pressrelease/details/86/2016-72/6130), the pass at the front is for a “Gold” loyalty status member, whereas the pass at the back is for a “standard” loyalty status member. This information can be represented in a Verifiable Credential, of course, but the example here uses the Apple Wallet / Google Wallet **formats to overlay a richer display**.

While it’s useful to have digital credentials that can be verified cryptographically, the reality is that there are often occasions when a quick “visual check” is done instead. For example, when at an airport, an airline staff member might visually check a mobile boarding pass to direct people to the correct queue they need to join. The mobile boarding pass _does_ get scanned at points like check-in, security, boarding etc., to digitally read the information, other scenarios where this is not done are equally valid. However, most Verifiable Credential formats do not explicitly provide such “human-friendly” forms of showing the data held in a credential.

### Documents

More broadly, there are other types of resources that might be relevant for companies beyond SSI vendors, that want a way to represent information about themselves in an immutable and trustworthy way.

Many companies require documentation such as Privacy Policies, Data Protection Policies or Terms of Use to be made publicly available. Moreover, [Trust over IP (ToIP) recommends making Governance Frameworks available through DID URLs](https://wiki.trustoverip.org/pages/viewpage.action?pageId=71241), which would typically be a text file, a [Markdown file](https://en.wikipedia.org/wiki/Markdown), PDF etc.

### Logos

Companies may want to provide authorised image logos to display across different websites, exchanges or block explorers. Examples of this include [key-publishing sites like Keybase.io](https://keybase.io/cheqd\_identity) (which is used by [Cosmos SDK block explorers such as our own](https://explorer.cheqd.io/validators) to show logos for validators) and “[favicons](https://en.wikipedia.org/wiki/Favicon)” (commonly used to set the logo for websites in browser tabs).

The current uses for resources are therefore very broad across the SSI ecosystem, and in addition, for other companies that may want to use DIDs to reference relevant information on ledger. For this reason, it is essential that the SSI community strengthens the way that resources are stored, referenced and retrieved in SSI ecosystems.

In our demo for IIW, we showed an IIW logo as a resource on-ledger, being used within the body of a Verifiable Credential. In the JSON below, you will be able to see the resources being used in both the **"@context"** and **"logo"** sections.

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://veramo.io/contexts/profile/v1",
    "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:z6jKUJA5YcZsNxZgsrQPKPipL2FRTf4s/resources/a20aa56a-a76f-4828-8a98-4c85d9494545"
  ],
  "type": [
    "VerifiableCredential",
    "EventReservation"
  ],
  "issuanceDate": "2022-11-17T03:19:03.328Z",
  "credentialSubject": {
    "id": "did:key:z6MkwTr46BWU42YejmZrDDCbL127dBoxSXeLuaHS75vTCg7i"
  },
  "reservationId": "4810116769",
  "reservationStatus": "https://schema.org/ReservationConfirmed",
  "reservationFor": {
    "@type": "Event",
    "name": "Internet Identity Workshop IIWXXXV",
    "startDate": "2022-11-16T16:00:00",
    "endDate": "2022-11-18T00:00:00",
    "location": "Computer History Museum, 1401 N Shoreline Blvd, Mountain View, CA 94043",
    "logo": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:z6jKUJA5YcZsNxZgsrQPKPipL2FRTf4s/resources/8140ec3a-d8bb-4f59-9784-a1cbf91a4a35"
  },
  "issuer": {
    "id": "did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX"
  },
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vdmVyYW1vLmlvL2NvbnRleHRzL3Byb2ZpbGUvdjEiLCJodHRwczovL3Jlc29sdmVyLmNoZXFkLm5ldC9kaWQ6Y2hlcWQ6dGVzdG5ldDp6NmpLVUpBNVljWnNOeFpnc3JRUEtQaXBMMkZSVGY0cy9yZXNvdXJjZXMvYTIwYWE1NmEtYTc2Zi00ODI4LThhOTgtNGM4NWQ5NDk0NTQ1Il0sInR5cGUiOlsiVmVyaWZpYWJsZUNyZWRlbnRpYWwiLCJFdmVudFJlc2VydmF0aW9uIl0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7ImlkIjoiZGlkOmtleTp6Nk1rd1RyNDZCV1U0Mlllam1ackREQ2JMMTI3ZEJveFNYZUx1YUhTNzV2VENnN2kifX0sIkBjb250ZXh0IjpbImh0dHBzOi8vd3d3LnczLm9yZy8yMDE4L2NyZWRlbnRpYWxzL3YxIiwiaHR0cHM6Ly92ZXJhbW8uaW8vY29udGV4dHMvcHJvZmlsZS92MSIsImh0dHBzOi8vcmVzb2x2ZXIuY2hlcWQubmV0L2RpZDpjaGVxZDp0ZXN0bmV0Ono2aktVSkE1WWNac054WmdzclFQS1BpcEwyRlJUZjRzL3Jlc291cmNlcy9hMjBhYTU2YS1hNzZmLTQ4MjgtOGE5OC00Yzg1ZDk0OTQ1NDUiXSwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIkV2ZW50UmVzZXJ2YXRpb24iXSwiaXNzdWFuY2VEYXRlIjoiMjAyMi0xMS0xN1QwMzoxOTowMy4zMjhaIiwiY3JlZGVudGlhbFN1YmplY3QiOnsiaWQiOiJkaWQ6a2V5Ono2TWt3VHI0NkJXVTQyWWVqbVpyRERDYkwxMjdkQm94U1hlTHVhSFM3NXZUQ2c3aSJ9LCJyZXNlcnZhdGlvbklkIjoiNDgxMDExNjc2OSIsInJlc2VydmF0aW9uU3RhdHVzIjoiaHR0cHM6Ly9zY2hlbWEub3JnL1Jlc2VydmF0aW9uQ29uZmlybWVkIiwicmVzZXJ2YXRpb25Gb3IiOnsiQHR5cGUiOiJFdmVudCIsIm5hbWUiOiJJbnRlcm5ldCBJZGVudGl0eSBXb3Jrc2hvcCBJSVdYWFhWIiwic3RhcnREYXRlIjoiMjAyMi0xMS0xNlQxNjowMDowMCIsImVuZERhdGUiOiIyMDIyLTExLTE4VDAwOjAwOjAwIiwibG9jYXRpb24iOiJDb21wdXRlciBIaXN0b3J5IE11c2V1bSwgMTQwMSBOIFNob3JlbGluZSBCbHZkLCBNb3VudGFpbiBWaWV3LCBDQSA5NDA0MyIsImxvZ28iOiJodHRwczovL3Jlc29sdmVyLmNoZXFkLm5ldC8xLjAvaWRlbnRpZmllcnMvZGlkOmNoZXFkOnRlc3RuZXQ6ejZqS1VKQTVZY1pzTnhaZ3NyUVBLUGlwTDJGUlRmNHMvcmVzb3VyY2VzLzgxNDBlYzNhLWQ4YmItNGY1OS05Nzg0LWExY2JmOTFhNGEzNSJ9LCJpc3N1ZXIiOnsiaWQiOiJkaWQ6Y2hlcWQ6bWFpbm5ldDp6QVh3d3FaemhDWkExTDc3WkJhOGZoVk5qTDlNUUNIWCJ9LCJzdWIiOiJkaWQ6a2V5Ono2TWt3VHI0NkJXVTQyWWVqbVpyRERDYkwxMjdkQm94U1hlTHVhSFM3NXZUQ2c3aSIsIm5iZiI6MTY2ODY1NTE0MywiaXNzIjoiZGlkOmNoZXFkOm1haW5uZXQ6ekFYd3dxWnpoQ1pBMUw3N1pCYThmaFZOakw5TVFDSFgifQ.U4vPbvdY7724a1jJwiDeCH_4_YC5sKUMcH6lY_XCVWBTE1RvYAnTj4fPHWMy6zSVFL9TAk4ZBOqFjKUtShBSCw"
  }
}
```

By clicking the image below, you will see that this is actually being pulled and resolved from the cheqd ledger:

{% embed url="https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:z6jKUJA5YcZsNxZgsrQPKPipL2FRTf4s/resources/8140ec3a-d8bb-4f59-9784-a1cbf91a4a35" %}
Image being pulled from the cheqd ledger using the resource module
{% endembed %}

## Rationale for storing resources on-ledger

### What are the problems with the way resources are stored?

There are multiple approaches to decentralised identity which rely on centralised infrastructure across different technical layers. **Decentralised Identifiers (DIDs):** are often stored on ledgers (e.g., [cheqd](https://github.com/cheqd/node-docs/blob/adr-008-resources-updates/architecture/adr-list/adr-002-cheqd-did-method.md), [Hyperledger Indy](https://hyperledger.github.io/indy-did-method/), distributed storage (e.g., [IPFS](https://ipfs.io/) in [Sidetree](https://identity.foundation/sidetree/spec/)), or non-ledger distributed systems (e.g., [KERI](https://keri.one/)). Yet, DIDs _can_ be stored on traditional centralised-storage endpoints (e.g., [_did:web_](https://w3c-ccg.github.io/did-method-web/), [_did:git_](https://github-did.com/)).

The issue of centralisation affects **resources** providing extra context and information to support Verifiable Credentials. These resources, such as **schemas** and **revocation lists**, are often stored and referenced using centralised hosting providers.

Using centralised hosting providers to store resources **may have a significant difference in the longevity and authenticity of Verifiable Credentials**. For example, a passport (which typically has a 5–10 year validity) issued as a Verifiable Credential anchored to a DID (regardless of whether the DID was on-ledger or not) might stop working if the credential schema, visual presentation format, or other necessary resources were stored off-ledger on traditional centralised storage.

* **DIDs could be tampered by compromising the hosting provider**: DIDs and DID Documents ("DIDDocs") stored at a centralised web endpoint can be compromised and replaced by malicious actors.
* **Hosting providers could unilaterally cease to host particular clients**: Hosting providers could terminate accounts due to factors such as non-payment of fees, violation of Terms of Service, etc.
* **Single point-of-failure in resiliency**: Even for highly-trusted and sophisticated hosting providers who may not present a risk of infrastructure being compromised, a service outage at the hosting provider can make a DID anchored on their systems inaccessible.

### **Single points of failure**

Even for highly-trusted and sophisticated hosting providers who may not present a risk of infrastructure being compromised, a service outage at the hosting provider can make a resource anchored on their systems inaccessible.

The high centralisation of cloud providers and history of noteworthy outages clearly demonstrates why we should not host resources on centralised cloud storage in production environments. In Q1 of 2022, the three largest players in the cloud (AWS, Google Cloud, Microsoft Azure) dominated with [65 per cent in nearly all regions (outside of China)](https://www.theregister.com/2022/05/02/cloud\_market\_share\_q1\_2022/).

<figure><img src="../../.gitbook/assets/Worlds cloud infrastructure image.png" alt="Image of cloud infrastructure rankings"><figcaption><p>Source: <a href="https://cointelegraph.com/news/the-future-of-the-internet-inside-the-race-for-web3-s-infrastructure">CoinTelegraph “The future of the internet: Inside the race for Web3’s infrastructure’</a></p></figcaption></figure>

_Figure 3: breakdown of global cloud providers market share_

Beyond cloud providers, there are other events that exemplify the issuers relying on larger players. The [Facebook outage of 2021](https://www.kentik.com/blog/facebooks-historic-outage-explained/) (shown in the graph below) [took down apps that used “Login with Facebook”](https://web.archive.org/web/20211005032128/https://www.wired.com/story/why-facebook-instagram-whatsapp-went-down-outage/) functionality. This highlights the risks of “contagion impact” (e.g., [a different Facebook outage took down Spotify, TikTok, Pinterest](https://www.engadget.com/facebook-sdk-spotify-tinder-tiktok-ios-outage-125806814.html)) of centralised digital systems — even ones run by extremely-capable tech providers.

<figure><img src="../../.gitbook/assets/On-ledger resources - Facebook global outage (1) (1) (1).png" alt="Image of global Facebook outage"><figcaption><p>Source: <a href="https://web.archive.org/web/20211005032128/https://www.wired.com/story/why-facebook-instagram-whatsapp-went-down-outage/">Why Facebook, Instagram, and WhatsApp All Went Down Today</a></p></figcaption></figure>

_Figure 4: Graph showing drop in Facebook traffic from their global service outage in 2021 (source:_ [_Kentik_](https://www.kentik.com/blog/facebooks-historic-outage-explained/)_)_

Likewise, with decentralised identity, there has been excellent work to _decentralise,_ with standards that remove the need for centralised intermediaries — notably around [Verifiable Credentials](https://www.w3.org/TR/vc-data-model/) and the decentralised trust provided by [DID Authentication](https://www.w3.org/TR/did-core/). Yet, all of this excellent work may be eroded in practice, unless every component of an SSI ecosystem is able to maintain an equivalent level of decentralised trust. Resources are currently an area that has been _centralised_ for the sake of convenience.

### **Link rot**

"Link rot" happens when over time, URLs become inaccessible, either because the endpoint where the content was stored is no longer active, or the URL format itself changes. The graph below from [an analysis by _The New York Times_ of linkrot](https://www.cjr.org/analysis/linkrot-content-drift-new-york-times.php) shows degradation over time of URLs.

<figure><img src="../../.gitbook/assets/On-ledger resources - Link Rot.jpeg" alt="Image of Link Rot over time"><figcaption><p>Source: <a href="https://www.cjr.org/analysis/linkrot-content-drift-new-york-times.php">What the ephemerality of the Web means for your hyperlinks</a></p></figcaption></figure>

_Figure 5: Linkrot analysis over 1996-2019 by New York Times (source:_ [_Columbia Journalism Review / New York Times_](https://www.cjr.org/analysis/linkrot-content-drift-new-york-times.php)_)_

For this reason, keeping an up-to-date version of the links themselves is crucial. Furthermore, a study of link rot found [at least 66.5% of links to sites in the last 9 years are dead](https://ahrefs.com/blog/link-rot-study/). This can have an adverse impact on the digital longevity of Verifiable Credentials if there’s “link rot” in the resources necessary to process the credential. For this reason, projects such as [The Internet Archive](https://archive.org/)’s [Wayback Machine](https://web.archive.org/) exist to snapshot digital ephemera before they are lost forever.

<figure><img src="../../.gitbook/assets/Why are links lost diagram.png" alt="Image of why links are lost"><figcaption><p>Source: <a href="https://ahrefs.com/blog/link-rot-study/">Ahrefs Study on Link Rot</a></p></figcaption></figure>

Figure 6: Diagram showing the various reasons why links are lost

This illustrates that link rot can affect a **significant proportion** of links in a relatively small amount of time, and once again, looking at how resources are currently stored in SSI ecosystems, if the resource locations are moved and the links are broken, the Verifiable Credentials relying on these resources become unusable. Therefore, resources, once defined, should be architected to be used and referenced indefinitely, without being changed.

### **Tamper-evident changes and censorship resistance**

Finally, the centralised way that resources are currently stored and managed is not immutable, and as a result, it is liable to tampering. For example, if a hosting provider is compromised, or if malicious actors are working for the company, resources may be changed and previous resource versions may be purged from the central database.

As we move towards a new web infrastructure with Web 3 ([and beyond…](https://t.co/KZ1zcSfyGW)), and as more projects leverage blockchain and distributed ledgers, it’s important not to port the previous issues of the web, and instead find novel ways to better manage information, with longevity in mind. This is why at cheqd, we have decided to redesign the way resources are captured on the ledger.

### Semantic Linkage

The way schemas are currently referenced from web URLs (e.g., schema.org/Person) is not immutable. Schemas may be changed and previous schema definitions may be purged from the database. This means if you had a Verifiable Credential issued in 2015 which referred to a particular schema in 2022, that schema definition may have significantly changed.

On the other hand, solutions that _do_ currently store schemas on ledger (e.g., Hyperledger Indy) don't have semantic linkage between old and new versions. In this instance, current ledgers allow new versions to be made but don't offer an easy way for client apps to discover linkages as they evolve over time.
