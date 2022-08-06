# 🧰 On-Ledger Resources

cheqd has built an **On-Ledger Resources Module** to extend the functionality of our decentralised identity network, providing capabilities not found on other self-sovereign identity networks.

A **Resource** is a blob of information stored on-ledger, discoverable through the same [Decentralized Identifier (DIDs) syntax](../decentralized-identifiers/README.md) that is widely used within the SSI industry.

## Use cases for on-ledger Resources

The use of Resources is potentially very broad; they can provide many different roles, such as:

* A **schema**, such as Camenisch-Lysyanskaya ("CL") schema ‘CL’, JSON-LD schema, JSON schema etc.
* A **visual representation**, such as [Overlays Capture Architecture (OCA)](https://oca.colossi.network/), [Apple Wallet PassKit](https://developer.apple.com/documentation/passkit), [Google Wallet Passes](https://developers.google.com/wallet), etc
* A **document**, such as [a Governance Framework](governance-framework.md)
* An **branding elements**, such as a company logo, brandmark, etc which can be pulled directly by block explorers and other Web3 applications
* A **revocation registry** for revoking already-issued credentials.

Through extending the use of DIDs to identify other on-ledger resources, trust can begin to be chained.

### New use cases enabled by Resources on-ledger

| Audience              | Quick wins                                                                                                                                                                                                                                                    | Longer term strategic objectives                                                                                                                                                                                                                                   |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **SSI Community**     | Create a much more **secure, resilient and decentralised format for storing schemas** than [schema.org](https://schema.org/)                                                                                                                                  | Lay the technical foundations for **supporting** [**AnonCreds**](https://hyperledger-indy.readthedocs.io/projects/sdk/en/latest/docs/design/002-anoncreds/README.html) **compatible Verifiable Credentials on cheqd**, in addition to support for JSON and JSON-LD |
| **Web 3.0 community** | Extend the [Trust over IP Resource Parameter Specification](https://wiki.trustoverip.org/display/HOME/DID+URL+Resource+Parameter+Specification), enabling **DIDs to act as #Web3 hyperlinks for any on (or off) ledger URL, Resource, file, or content** | Enable Web3 Companies to use on-ledger Resources to **fetch company information (such as logos, token info, relevant token APIs)** to **populate Block Explorers and Exchanges**, rather than through Keybase or manual processes.                                 |

> You can think of it as a hyperlink for #Web3. But unlike a hyperlink, it is possible to specify the type of resource to be retrieved, and reject anything unexpected.
