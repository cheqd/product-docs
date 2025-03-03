# Identity Write Pricing

> Note: the prices listed below are subject to change, as they are parameters controlled by community governance mechanisms.

## cheqd Identity Write Pricing

Below are a list of prices in CHEQ and approximate dollar values for carrying our identity writes to the network:

| Metric                        | Amount CHEQ | Approximate cost (USD) |
| ----------------------------- | ----------- | ---------------------- |
| **DID write**                 | 50          | \~$2.00                |
| **DID update**                | 25          | \~$1.00                |
| **DID deactivate**            | 10          | \~$0.40                |
| **Resource create (default)** | 5           | \~$0.20                |
| **Resource create (JSON)**    | 2.5         | \~$0.10                |
| **Resource create (image)**   | 10          | \~$0.40                |

cheqd Identity pricing detailed above was implemented on-ledger as part of the January 2023 release - [v1.0.x](https://product.cheqd.io/network/upgrades/2021/v0.1.x)​.

For more information on cheqd's Identity Transaction pricing, see our latest Tokenomics - [cheqd’s tokenomics for SSI explained: part 4](https://blog.cheqd.io/cheqds-tokenomics-for-ssi-explained-part-4-3f4c6a9ea1c0).

## Comparison with other networks <a href="#comparison-with-other-networks" id="comparison-with-other-networks"></a>

| Metric                                      | cheqd Testnet | cheqd Mainnet        | Sovrin StagingNet | Sovrin MainNet | Indicio   |
| ------------------------------------------- | ------------- | -------------------- | ----------------- | -------------- | --------- |
| **DID write**                               | Free          | \~$2.00 _(50 CHEQ)_  | $1.00             | $10.00         | $5000 p/a |
| **DID update**                              | Free          | \~$1.00 _(25 CHEQ)_  | $1.00             | $10.00         | $5000 p/a |
| **DID deactivate**                          | Free          | \~$0.40 _(10 CHEQ)_  | $1.00             | $10.00         | $5000 p/a |
| **Resource create (schema)**                | Free          | \~$0.10 _(2.5 CHEQ)_ | $5.00             | $50.00         | $5000 p/a |
| **Resource create (credential definition)** | Free          | \~$0.10 _(2.5 CHEQ)_ | $2.50             | $25.00         | $5000 p/a |
| **Resource create (status list)**           | Free          | \~$0.10 _(2.5 CHEQ)_ | $2.00             | $20.00         | $5000 p/a |
| **Resource create (status list entry)**     | Free          | \~$0.10 _(2.5 CHEQ)_ | $0.01             | $0.10          | $5000 p/a |

## Get started

There are plenty of places you can go to get started creating cheqd DIDs. Simply pick the option which is best suited for your needs:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Credential Service</strong></mark></td><td><strong>Credential Service</strong> is a SaaS product offering the easiest and quickest way to issue Credentials, using REST APIs and very little integration effort.</td><td><a href="broken-reference">Broken link</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td><a href="../../advanced/did-registrar/">did-registrar</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo SDK Plugin</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td><a href="../../sdk/veramo-plugin/">veramo-plugin</a></td></tr><tr><td><mark style="color:blue;"><strong>Aries Framework JavaScript</strong></mark></td><td>Aries Framework JavaScript is an SDK which <a href="https://hyperledger.github.io/anoncreds-spec/"><strong>supports ZKCreds (AnonCreds)</strong></a> natively with cheqd support. </td><td><a href="../../sdk/credo/">credo</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id SSI Kit</strong></mark></td><td>Walt.id SSI Kit is an SDK that supports the <a href="https://digital-strategy.ec.europa.eu/en/library/european-digital-identity-architecture-and-reference-framework-outline"><strong>European Architecture and Reference Framework (ARF)</strong></a> standards for identity, with full cheqd support. </td><td><a href="../../sdk/ssi-kit.md">ssi-kit.md</a></td></tr><tr><td><mark style="color:blue;"><strong>cheqd Cosmos CLI</strong></mark></td><td>Cosmos CLI which directly communicates with the cheqd network. This should only be used for <strong>testing environments</strong>.</td><td><a href="../../advanced/tooling/cheqd-node-cli/">cheqd-node-cli</a></td></tr></tbody></table>
