# 📦 Software Development Kits (SDKs)

One of cheqd's primary motives is to make itself accessible to the widest set of identity applications possible. To accomplish this, cheqd has built a **flexible set of packages and tooling** to accommodate its identity capabilities into a **broad set of external SDKs and applications**.&#x20;

{% hint style="info" %}
:books: **Learn about SDKs**

Learn more about [SDKs, libraries and platforms on our learn site here](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/sdks-libraries-and-platforms)**.**
{% endhint %}

## Understanding cheqd's SDK and package composition

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

## Moderate complexity: Veramo SDK plugin for cheqd

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

## High complexity: cheqd SDK

The [cheqd SDK](https://github.com/cheqd/sdk) plugs directly into cheqd Network and is responsible for communicating with the core ledger code. Therefore, the cheqd SDK plays the role of handling:

* [TypeScript Protobuf definitions](https://github.com/cheqd/ts-proto) for custom cheqd Cosmos SDK modules (DID Module and Resource Module)
* [CosmJS](https://github.com/cosmos/cosmjs), for base Cosmos SDK module functions

Owing to this, the cheqd SDK requires a high amount of complexity to integrate directly.&#x20;

<figure><img src="../../.gitbook/assets/cheqd SDK package.png" alt=""><figcaption><p>cheqd SDK package diagram</p></figcaption></figure>

## Supported SDKs

cheqd are constantly expanding the amount of SDKs supported in order to support multiple credential types and protocols.

You can take a look at which [specific protocols are covered by which SDKs on our Product site here](https://product.cheqd.io/products/toolbox).

* [Veramo SDK for cheqd](veramo-sdk-for-cheqd/)
* [walt.id SSI Kit](https://docs.walt.id/v/ssikit/ecosystems/cheqd)
* [Hyperledger Aries Framework JavaScript](hyperledger-aries-framework-javascript.md)
