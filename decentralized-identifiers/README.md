# 🆔 DID Method

The [cheqd DID method](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method) is a [W3C Decentralized Identifiers (DID)](https://www.w3.org/TR/did-core/#dfn-decentralized-identifiers) specification-compliant DID method.

The team behind the core cheqd product/tech stack have extensive experience working in [self-sovereign identity (SSI) / decentralized identity](https://learn.cheqd.io/overview/introduction-to-decentralised-identity). Many of the design decisions and product features available on cheqd network are meant to address real-world issues with SSI adoption that the team faced.

> **Learn more about the `did:cheqd` method**
>
> Details of the `did:cheqd` method are described in the [Architecture Decision Record for the cheqd DID method](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method)

## What's *different* about the cheqd DID method

* Built using the [Cosmos SDK framework](https://cosmos.network/), which allows for high-scalability and fault-tolerance due to its [Tendermint consensus protocol](https://tendermint.com/), and integration with a wide range of [inter-blockchain communication](https://ibcprotocol.org/). You can [read more about why cheqd decided to build using Cosmos SDK](https://blog.cheqd.io/why-cheqd-has-joined-the-cosmos-4db8845722c5) (instead of Indy, Ethereum, Polkadot, Solana etc).
* Writing cheqd DIDs and DIDDocs costs significantly less and consumes lower computationtal resources than other popular blockchains used for digital identity use cases (such as [Hyperledger Indy](https://wiki.hyperledger.org/display/indy)). The run cost of a cheqd network ledger can be 50-60% cheaper than an Indy network of equivalent number of nodes.
* [Universal Resolver support](../../architecture/adr-list/adr-001-did-resolver.md) provides an easy way to integrate cheqd into SSI and non-SSI applications.
* We aim to provide backwards compatibility with client applications designed for Hyperledger Indy (`did:indy:` and `did:sov:` methods) through a blend of [on-ledger functionality such as Resources](../resources/README.md) and SDKs while *also* providing a future-compatible mode with more complex DIDDocs than those possible on Hyperledger Indy.

## W3C DID Specification compliance

### DID Spec Registries

You can also find the cheqd DID method formally registered in the [W3C DID Spec Registries](https://www.w3.org/TR/did-spec-registries/#did-methods), the canonical registry for DID methods.

### DID Core Test suite

Standards-compliance tests with the [W3C DID Core Test suite](https://w3c.github.io/did-test-suite/) is also [available and updated regularly for `did:cheqd`](https://w3c.github.io/did-test-suite/#M8).

There are three components that the DID Core Test suite checks for specification compliance: DID Methods, DID Resolvers, and DID URL Dereferencers. These can be viewed at:

* **DID Methods**: [`did:cheqd` Test suite results](https://github.com/w3c/did-test-suite/blob/main/packages/did-core-test-server/suites/implementations/did-cheqd.json)
* **DID Resolution**: [cheqd DID Resolver test results](https://github.com/w3c/did-test-suite/blob/main/packages/did-core-test-server/suites/implementations/resolver-did-cheqd.json)
* **DID URL Dereferencing**: [cheqd DID Resolver dereferencing tests](https://github.com/w3c/did-test-suite/blob/main/packages/did-core-test-server/suites/implementations/dereferencer-cheqd.json)
