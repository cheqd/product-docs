# cheqd DID Method

## Overview

[DID methods](https://www.w3.org/TR/did-core/#dfn-did-methods) are the mechanism by which a particular type of [DID](https://www.w3.org/TR/did-core/#dfn-decentralized-identifiers) and its associated [DID document](https://www.w3.org/TR/did-core/#dfn-did-documents) are created, resolved, updated, and deactivated.

Please find our [cheqd DID method architecture decision record here](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method).

## Why is the cheqd DID Method notable?

The cheqd DID method enables:

* Storage of cheqd DID Doc in cheqd blockchain
* BFT(Tendermint) Consensus protocol is used in the cheqd ledger, in addition, Proof of Stake (PoS) is used with its own tokenomics
* Writing cheqd DIDs and DID Docs to the ledger costs significantly less than other popular blockchains, 50-60% cheaper than an Indy network of equivalent number of nodes because the compute requirements / associated costs are far lower than an equivalent Indy node
* By itself, the cheqd DID Method includes all the necessary sections from the specification and strictly complies with it
* Universal Resolver support makes cheqd DID method compatible with many SSI applications by default
* cheqd DID Method is flexible and allows some extensions. This will soon allow storing in cheqd ledger all the information necessary for issuing credentials for a long list of different protocols.
* The first method and ledger which will provide backwards compatibility with Hyperledger Indy (`did:indy:` and `did:sov:` methods) oriented applications, (such as single verkey, simple DIDDocs etc), while also giving a forwards compatible mode that’s more in-line with new specs.

## W3C DID Spec Registries

You can also find the cheqd DID method formally registered in the [W3C DID Spec Registries](https://www.w3.org/TR/did-spec-registries/).

The DID Spec Registries acts as a canonical place for on-ledger implementation standards, including DID Methods.  

See cheqd's did method at [did:cheqd](https://github.com/w3c/did-test-suite/blob/main/packages/did-core-test-server/suites/implementations/did-cheqd.json)