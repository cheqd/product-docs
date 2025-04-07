---
description: Understand cheqd's SDK suite and choose the best fit for your project.
---

# Choosing the right SDK

## Integrate with a cheqd supported SDK

cheqd has integrated into a series of SDKs to enable third parties to create DIDs and resources, issue and verify Verifiable Credentials, using cheqd DIDs, DID Documents and Schemas.

## Choose a software stack to suit your needs

cheqd is continually integrating into different software development kits to suit the needs of different partners and their clients.

Below is a comparison between our supported SDKs, including: Credo (formerly Aries Framework JavaScript), ACA-Py, Veramo SDK plugin and Walt.id's Community Stack.

| Functionality                                                                                                                                            | Veramo     | Credo | Walt.id SSI Kit |   ACA-Py   |
| -------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | :---: | :-------------: | :--------: |
| **Create cheqd DIDs (**[**ADR**](../architecture/adr-list/adr-001-cheqd-did-method.md)**)**                                                              | ✔️         |   ✔️  |        ✔️       |     ✔️     |
| **cheqd Credential Payments**                                                                                                                            | ✔️         |   ❌   |        ❌        |     ✔️     |
| **JSON based JWT Verifiable Credential (**[**spec**](https://www.w3.org/TR/vc-data-model/)**)**                                                          | ✔️         |   ❌   |        ✔️       |     ✔️     |
| **JSON-LD Verifiable Credential (**[**spec**](https://www.w3.org/TR/vc-data-model/)**)**                                                                 | ✔️         |   ✔️  |        ✔️       |     ✔️     |
| **AnonCreds (**[**spec**](https://hyperledger.github.io/anoncreds-spec/)**)**                                                                            | ❌          |   ✔️  |        ❌        |     ✔️     |
| **Selective Disclosure-JWT Credential (**[**spec**](https://datatracker.ietf.org/doc/draft-ietf-oauth-selective-disclosure-jwt/)**)**                    | ⌛(roadmap) |   ✔️  |        ✔️       |     ✔️     |
| **Create on-ledger Schema (**[**ADR**](../architecture/adr-list/adr-002-did-linked-resources.md)**)**                                                    | ✔️         |   ✔️  |        ❌        |     ✔️     |
| **Create Credential Definition (**[**spec**](../advanced/anoncreds/)**)**                                                                                | ❌          |   ✔️  |        ❌        |     ✔️     |
| **DIDComm v1.0 (**[**spec**](https://didcomm.org/basicmessage/1.0/)**)**                                                                                 | ✔️         |   ✔️  |        ❌        |     ✔️     |
| **DIDComm v2.0 (**[**spec**](https://identity.foundation/didcomm-messaging/spec/)**)**                                                                   | ✔️         |   ✔️  |        ❌        |     ✔️     |
| **DID Exchange Protocol (**[**RFC 0023**](https://github.com/hyperledger/aries-rfcs/tree/main/features/0023-did-exchange)**)**                           | ❌          |   ✔️  |        ❌        |     ✔️     |
| **Agent Connection Protocol (**[**RFC 0160**](https://github.com/hyperledger/aries-rfcs/blob/main/features/0160-connection-protocol/README.md)**)**      | ❌          |   ✔️  |        ❌        |     ✔️     |
| **Out of Band Protocol (**[**RFC 0434**](https://github.com/hyperledger/aries-rfcs/blob/main/features/0434-outofband/README.md)**)**                     | ❌          |   ✔️  |        ❌        |     ✔️     |
| **Self Issued OpenID Provider v2 (OIDC-SIOP) (**[**spec**](https://openid.net/specs/openid-connect-self-issued-v2-1_0.html)**)**                         | ⌛(roadmap) |   ✔️  |        ✔️       |      ❌     |
| **OpenID for Verifiable Credential Issuance (**[**spec**](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html)**)**                | ⌛(roadmap) |   ✔️  |        ✔️       |      ❌     |
| **OpenID for Verifiable Credential Presentations (**[**spec**](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html)**)**                 | ⌛(roadmap) |   ✔️  |        ✔️       |      ❌     |
| **Status List v2021 Revocation (**[**spec**](https://www.w3.org/TR/vc-status-list/)**)**                                                                 | ✔️         |   ❌   |        ✔️       |     ✔️     |
| **Token Status List**                                                                                                                                    | ❌          |   ✔️  |        ✔️       |     ✔️     |
| **AnonCreds Revocation Registry Definitions (**[**spec**](https://docs.cheqd.io/identity/guides/anoncreds/revocation-registry-definition)**)**           | ❌          |   ✔️  |        ❌        |     ✔️     |
| **AnonCreds Status List Entries (**[**spec**](https://docs.cheqd.io/identity/guides/anoncreds/revocation-status-list)**)**                               | ❌          |   ✔️  |        ❌        |     ✔️     |
| **Issue Credential Protocol (**[**RFC 0036**](https://github.com/hyperledger/aries-rfcs/blob/master/features/0036-issue-credential/README.md)**)**       | ❌          |   ✔️  |        ❌        |     ✔️     |
| **Issue Credential Protocol V2 (**[**RFC 0453**](https://github.com/hyperledger/aries-rfcs/blob/master/features/0453-issue-credential-v2/README.md)**)** | ❌          |   ✔️  |        ❌        |     ✔️     |
| **DIF Presentation Exchange (**[**spec**](https://identity.foundation/presentation-exchange/)**)**                                                       | ✔️         |   ✔️  |        ✔️       |     ✔️     |
| **Aries Present Proof 2.0 (**[**spec**](https://github.com/hyperledger/aries-rfcs/blob/main/features/0454-present-proof-v2/README.md)**)**               | ❌          |   ✔️  |        ❌        |     ✔️     |
| **DID-Linked Trust Registries**                                                                                                                          | ⌛(roadmap) |   ❌   |        ❌        | ⌛(roadmap) |

## Get started

Choose one of our SDKs below and started building cheqd into your existing or new product:

<table data-card-size="large" data-view="cards" data-full-width="false"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Credo</strong></mark></td><td>Credo is an SDK that is building directly towards supporting the EUDI standards, as well as support for AnonCreds.</td><td></td><td><a href="../.gitbook/assets/Credo.png">Credo.png</a></td><td><a href="credo/">credo</a></td></tr><tr><td><mark style="color:blue;"><strong>ACA-Py</strong></mark></td><td>ACA-Py is a quickly expanding SDK, moving from the AnonCreds ecosystem towards wider support for EUDI.</td><td></td><td><a href="../.gitbook/assets/ACA-Py.png">ACA-Py.png</a></td><td><a href="aca-py/">aca-py</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td></td><td><a href="../.gitbook/assets/veramo logo.png">veramo logo.png</a></td><td><a href="veramo-plugin/">veramo-plugin</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id Community Stack</strong></mark></td><td>Walt.id Community Stack is an SDK that supports the <a href="https://digital-strategy.ec.europa.eu/en/library/european-digital-identity-architecture-and-reference-framework-outline"><strong>European Architecture and Reference Framework (ARF)</strong></a> standards for identity, with cheqd DID support.</td><td></td><td><a href="../.gitbook/assets/walt.id logo.png">walt.id logo.png</a></td><td><a href="walt-id.md">walt-id.md</a></td></tr></tbody></table>
