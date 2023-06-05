# 📦 Software Development Kits (SDKs)

## Integrate with cheqd

cheqd has created simple tooling to enable third parties to create DIDs and resources, issue and verify Verifiable Credentials, using cheqd DIDs, DID Documents and Schemas.

## Choose a software stack to suit your needs

cheqd is continually integrating into different software development kits to suit the needs of different partners and their clients.

Below is a comparison between our three initial supported SDKs, Veramo SDK for cheqd, Aries Framework JavaScript and Walt.id's SSI Kit.

| Functionality                                                                                                                                            | Veramo SDK for cheqd | Aries Framework JavaScript for cheqd | Walt.id SSI Kit |
| -------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------: | :----------------------------------: | :-------------: |
| **Create cheqd DIDs (**[**ADR**](../../architecture/adr-list/adr-001-cheqd-did-method.md)**)**                                                           |          ✔️          |                  ✔️                  |        ✔️       |
| **JSON based JWT Verifiable Credential (**[**spec**](https://www.w3.org/TR/vc-data-model/)**)**                                                          |          ✔️          |                   ❌                  |        ✔️       |
| **JSON-LD Verifiable Credential (**[**spec**](https://www.w3.org/TR/vc-data-model/)**)**                                                                 |          ✔️          |              ⌛(roadmap)              |        ✔️       |
| **AnonCreds (**[**spec**](https://hyperledger.github.io/anoncreds-spec/)**)**                                                                            |           ❌          |                  ✔️                  |        ❌        |
| **Selective Disclosure-JWT Credential (**[**spec**](https://datatracker.ietf.org/doc/draft-ietf-oauth-selective-disclosure-jwt/)**)**                    |      ⌛(roadmap)      |                   ❌                  |        ✔️       |
| **Create on-ledger Schema (**[**ADR**](../../architecture/adr-list/adr-002-did-linked-resources.md)**)**                                                 |          ✔️          |                  ✔️                  |        ❌        |
| **Create Credential Definition (**[**spec**](../anoncreds/)**)**                                                                                         |           ❌          |                  ✔️                  |        ❌        |
| **DIDComm v1.0 (**[**spec**](https://didcomm.org/basicmessage/1.0/)**)**                                                                                 |          ✔️          |                  ✔️                  |        ❌        |
| **DIDComm v2.0 (**[**spec**](https://identity.foundation/didcomm-messaging/spec/)**)**                                                                   |          ✔️          |                  ✔️                  |        ❌        |
| **DID Exchange Protocol (**[**RFC 0023**](https://github.com/hyperledger/aries-rfcs/tree/main/features/0023-did-exchange)**)**                           |           ❌          |                  ✔️                  |        ❌        |
| **Agent Connection Protocol (**[**RFC 0160**](https://github.com/hyperledger/aries-rfcs/blob/main/features/0160-connection-protocol/README.md)**)**      |           ❌          |                  ✔️                  |        ❌        |
| **Out of Band Protocol (**[**RFC 0434**](https://github.com/hyperledger/aries-rfcs/blob/main/features/0434-outofband/README.md)**)**                     |           ❌          |                  ✔️                  |        ❌        |
| **Self Issued OpenID Provider v2 (OIDC-SIOP) (**[**spec**](https://openid.net/specs/openid-connect-self-issued-v2-1\_0.html)**)**                        |      ⌛(roadmap)      |                   ❌                  |        ✔️       |
| **OpenID for Verifiable Credential Issuance (**[**spec**](https://openid.net/specs/openid-4-verifiable-credential-issuance-1\_0.html)**)**               |      ⌛(roadmap)      |              ⌛(roadmap)              |        ✔️       |
| **OpenID for Verifiable Credential Presentations (**[**spec**](https://openid.net/specs/openid-4-verifiable-presentations-1\_0.html)**)**                |      ⌛(roadmap)      |              ⌛(roadmap)              |        ✔️       |
| **Status List 2021 Revocation (**[**spec**](https://www.w3.org/TR/vc-status-list/)**)**                                                                  |          ✔️          |                   ❌                  |        ✔️       |
| **AnonCreds Revocation Registry Definitions (**[**spec**](https://docs.cheqd.io/identity/guides/anoncreds/revocation-registry-definition)**)**           |           ❌          |                  ✔️                  |        ❌        |
| **AnonCreds Status List Entries (**[**spec**](https://docs.cheqd.io/identity/guides/anoncreds/revocation-status-list)**)**                               |           ❌          |                  ✔️                  |        ❌        |
| **Issue Credential Protocol (**[**RFC 0036**](https://github.com/hyperledger/aries-rfcs/blob/master/features/0036-issue-credential/README.md)**)**       |           ❌          |                  ✔️                  |        ❌        |
| **Issue Credential Protocol V2 (**[**RFC 0453**](https://github.com/hyperledger/aries-rfcs/blob/master/features/0453-issue-credential-v2/README.md)**)** |           ❌          |                  ✔️                  |        ❌        |
| **DIF Presentation Exchange (**[**spec**](https://identity.foundation/presentation-exchange/)**)**                                                       |          ✔️          |                   ❌                  |        ✔️       |
| **Aries Present Proof 2.0 (**[**spec**](https://github.com/hyperledger/aries-rfcs/blob/main/features/0454-present-proof-v2/README.md)**)**               |           ❌          |                  ✔️                  |        ❌        |
| **DID-Linked Trust Registries**                                                                                                                          |      ⌛(roadmap)      |                   ❌                  |        ❌        |



## Understanding the structure of cheqd's packages

One of cheqd's primary motives is to make itself accessible to the widest set of identity applications possible. To accomplish this, cheqd has built a **flexible set of packages and tooling** to accommodate its identity capabilities into a **broad set of external SDKs and applications**.&#x20;

cheqd maintains an array of packages with **varying levels of integration complexity** to allow its partners and customers to have a variety of ways of plugging into cheqd's identity functionality.

<figure><img src="../../.gitbook/assets/cheqd SDK packages.png" alt=""><figcaption><p>cheqd SDK packages diagram</p></figcaption></figure>

## Low complexity:&#x20;

### 1. DID Registrar and Credential Service

For customers and partners that do not want to directly integrate cheqd's packages directly but would prefer a more lightweight approach, we provide a set of APIs for:

#### DID Registrar:

* **DIDs:** Creating, updating and deactivating&#x20;
* **Keys**: Creating and listing
* **DID-Linked Resources**: Creating
  * **StatusList**: Create/update
  * **Schema**: Create/update

#### Credential Service:

* All of the above, and additionally:
* **Credentials**: Issuing, verifying and revoking

Using these APIs, customers can easily integrate cheqd's identity functionality into their existing applications through hitting simple REST APIs.&#x20;

### 2. External identity applications

#### Walt.id SSI Kit

[SSI Kit is an Open Source SDK from walt.id](https://walt.id/ssi-kit) which allows cheqd functionality, and other identity networks, to be used through API calls or through a CLI tool. &#x20;

Walt.id run multiple SaaS offering for customers with varying levels of support and integration requirements.

#### GoDiddy

[GoDiddy](https://godiddy.com/) is a product from [DanubeTech](https://danubetech.com/) which uses both the Universal Registrar and Universal Resolver interfaces to provide an enterprise-level service for customers to create, update and resolve DIDs across a variety of identity networks, including did:cheqd.

## Moderate complexity:&#x20;

### 1. Veramo SDK plugin for cheqd

The [Veramo SDK](https://github.com/uport-project/veramo) is an agent designed to handle multiple networks and DID methods. Current implementations of the Veramo SDK include _did:ethr, did:web, did:key_ and more.

We chose to build a cheqd plugin into Veramo SDk for a few key reasons.

1. **Design Principles —** The Veramo SDK was designed to be highly flexible and modular making it highly scalable and fit for a lot of complex workflows. As a result, we felt it offered a route to minimise how much needs to be built from scratch. Through its flexible plug-in system, it’s easy to pick and choose which plug-ins are most beneficial, plus it’s possible to add in our custom packages where required which we knew would be necessary from Cosmos-based transactions.
2. **Developer Experience** — The Veramo SDK has been designed in a way that offers a fast end-to-end process. Ultimately, at cheqd, we want to reduce the amount of time our team spends on SDKs and so we can maintain our focus on building ledger functionality (i.e. building our implementation of the revocation registry and the credentials payment rails).
3. **Attractive & Simple CLI —** The Veramo core API is exposed by its CLI which makes it easy to create DIDs and VCs directly from a terminal or local cloud agent.
4. **Platform Agnostic** — The Veramo packages run on Node, Browsers and React & React Native right out of the box.

Veramo provides an excellent foundation for clients that want to build verifiable data applications. This is because Veramo Core, the Veramo CLI and any specific plugins are available as NPM packages, enabling:

1. Identity functionality to be carried out through a **native CLI interface**; or
2. Identity functionality to be **integrated directly into client applications through NPM packages**.

Using the DID-Provider-cheqd package, we have also been able to **reuse identity functionality** and abstract the complexity of cheqd/SDK and cheqd Network in less complex packages (see above).

### 2. Aries Framework JavaScript for cheqd&#x20;

Aries Framework JavaScript is a framework written in TypeScript for building **SSI Agents and DIDComm services** that aims to be **compliant and interoperable** with the standards defined in the [Aries RFCs](https://github.com/hyperledger/aries-rfcs).

cheqd is now fully supported in AFJ and can be used for the issuance of AnonCreds as well as the creation of Credential Schemas and Credential Definitions.&#x20;

AnonCreds using cheqd are facilitated using the [cheqd AnonCreds Object Method](../anoncreds/).

## High complexity

### 1. cheqd SDK

The [cheqd SDK](https://github.com/cheqd/sdk) plugs directly into cheqd Network and is responsible for communicating with the core ledger code. Therefore, the cheqd SDK plays the role of handling:

* [TypeScript Protobuf definitions](https://github.com/cheqd/ts-proto) for custom cheqd Cosmos SDK modules (DID Module and Resource Module)
* [CosmJS](https://github.com/cosmos/cosmjs), for base Cosmos SDK module functions

Owing to this, the cheqd SDK requires a high amount of complexity to integrate directly.&#x20;

<figure><img src="../../.gitbook/assets/cheqd SDK package.png" alt=""><figcaption><p>cheqd SDK package diagram</p></figcaption></figure>

##
