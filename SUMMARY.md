# Table of contents

## ℹ Getting Started

* [Product Overview](README.md)

## 🟢 Start Issuing Credentials <a href="#credential-service" id="credential-service"></a>

* [Get started with Credential Service](credential-service/get-started/README.md)
  * [🔄 Advanced configuration options](credential-service/get-started/advanced.md)
* [👉 Set up your account](credential-service/set-up-account.md)
* [✅ Issue Credentials and Presentations](credential-service/credentials/README.md)
  * [Issue Credential](credential-service/credentials/issue-credential.md)
  * [Verify Credential](credential-service/credentials/verify-credential.md)
  * [Verify Presentation](credential-service/credentials/verify-presentation.md)
  * [Revoke Credential](credential-service/credentials/revoke-credential.md)
  * [Suspend or Unsuspend Credential](credential-service/credentials/suspend-credential.md)
* [♻ Charge for Verifiable Credentials](credential-service/payments/README.md)
  * [Charge for Status List](credential-service/payments/charge-for-status-list.md)
  * [Understanding Access Control Conditions](credential-service/payments/payment-conditions.md)
* [🆔 Create DIDs and Identity keys](credential-service/dids/README.md)
  * [Create Issuer DID](credential-service/dids/create-did.md)
  * [Create Identity Keys and Subject DIDs](credential-service/dids/create-subject-did.md)
  * [Resolve a DID](credential-service/dids/resolve-did.md)
  * [Update or Deactivate DID](credential-service/dids/update-did.md)
* [🎋 Create Status Lists](credential-service/status-lists/README.md)
  * [Create and Publish Status List](credential-service/status-lists/create-status-list.md)
  * [Update Status List](credential-service/status-lists/update-status-list.md)
  * [Query Status List](credential-service/status-lists/query-status.md)
* [↕ Create DID-Linked Resources](credential-service/did-linked-resources/README.md)
  * [Create DID-Linked Resource](credential-service/did-linked-resources/create-dlr.md)
  * [Understanding DID-Linked Resources](credential-service/did-linked-resources/understanding-dlrs/README.md)
    * [Context for developing DID-Linked Resources](credential-service/did-linked-resources/understanding-dlrs/context.md)
    * [Technical composition of DID-Linked Resources](credential-service/did-linked-resources/understanding-dlrs/technical-composition.md)
    * [Referencing DID-Linked Resources in VCs](credential-service/did-linked-resources/understanding-dlrs/resources-in-credentials.md)

## 🛠 Integrate an SDK <a href="#sdk" id="sdk"></a>

* [Choosing the right SDK](sdk/understanding-sdks.md)
* [🍈 Veramo SDK Plugin](sdk/veramo-plugin/README.md)
  * [Setting up Veramo CLI for cheqd](sdk/veramo-plugin/setup/README.md)
    * [Troubleshooting Veramo CLI setup](sdk/veramo-plugin/setup/troubleshooting.md)
  * [Verifiable Credentials and Presentations](sdk/veramo-plugin/credentials-and-presentations/README.md)
    * [Issue a Verifiable Credential](sdk/veramo-plugin/credentials-and-presentations/issue-credential.md)
    * [Verify a Verifiable Credential](sdk/veramo-plugin/credentials-and-presentations/verify-jwt-credential.md)
    * [Create a Verifiable Presentation](sdk/veramo-plugin/credentials-and-presentations/create-jwt-presentation.md)
    * [Verify a Verifiable Presentation](sdk/veramo-plugin/credentials-and-presentations/verify-jwt-presentation.md)
  * [Credential Payments](sdk/veramo-plugin/credential-payments.md)
  * [Decentralised Identifiers (DIDs)](sdk/veramo-plugin/did-operations/README.md)
    * [Create a DID](sdk/veramo-plugin/did-operations/create-did.md)
    * [Querying a DID](sdk/veramo-plugin/did-operations/query-did.md)
    * [Update an existing DID](sdk/veramo-plugin/did-operations/update-did.md)
    * [Deactivate a DID](sdk/veramo-plugin/did-operations/deactivate-did.md)
    * [Create an off-ledger holder DID](sdk/veramo-plugin/did-operations/create-subject-did.md)
    * [Managing identity keys](sdk/veramo-plugin/did-operations/identity-keys.md)
    * [Troubleshooting](sdk/veramo-plugin/did-operations/troubleshooting.md)
  * [Status List v2021](sdk/veramo-plugin/status-list-v2021/README.md)
    * [Status List v2021 on cheqd](sdk/veramo-plugin/status-list-v2021/statuslist2021-resources.md)
    * [Issuing a Verifiable Credential referencing Status List](sdk/veramo-plugin/status-list-v2021/issuing-a-verifiable-credential-referencing-status-list.md)
  * [DID-Linked Resources](sdk/veramo-plugin/did-linked-resources/README.md)
    * [Create a DID-Linked Resource](sdk/veramo-plugin/did-linked-resources/create-resource.md)
    * [Create a new Resource version within existing Collection](sdk/veramo-plugin/did-linked-resources/create-new-version.md)
* [🍏 Aries Framework JavaScript](sdk/aries-framework-js.md)
* [🫐 Walt.id SSI Kit](sdk/ssi-kit.md)

## 🏗 Architecture

* [Architecture Decision Record (ADR) Process](architecture/README.md)
* [List of ADRs](architecture/adr-list/README.md)
  * [1⃣ ADR 001: cheqd DID Method](architecture/adr-list/adr-001-cheqd-did-method.md)
  * [2⃣ ADR 002: DID-Linked Resources](architecture/adr-list/adr-002-did-linked-resources.md)
  * [3⃣ ADR 003: DID Resolver](architecture/adr-list/adr-003-did-resolver.md)
  * [4⃣ ADR 004: DID Registrar](architecture/adr-list/adr-004-did-registrar.md)
  * [5⃣ ADR 005: DID Resolution & DID URL Dereferencing](architecture/adr-list/adr-005-did-resolution-and-did-url-dereferencing.md)

## 💫 Advanced features and alternatives

* [➡ DID Registrar](advanced-features-and-alternatives/did-registrar/README.md)
  * [Setup DID Registrar](advanced-features-and-alternatives/did-registrar/did-registrar-setup.md)
  * [Create a DID](advanced-features-and-alternatives/did-registrar/create-did.md)
  * [Create a DID-Linked Resource](advanced-features-and-alternatives/did-registrar/create-resource.md)
* [⬅ DID Resolver](advanced-features-and-alternatives/did-resolver/README.md)
  * [Setup DID Resolver](advanced-features-and-alternatives/did-resolver/setup-did-resolver.md)
* [🔒 AnonCreds on cheqd](advanced-features-and-alternatives/anoncreds/README.md)
  * [Schemas](advanced-features-and-alternatives/anoncreds/schema.md)
  * [Credential Definitions ("CredDefs")](advanced-features-and-alternatives/anoncreds/credential-definition.md)
  * [Revocation Registry Definitions](advanced-features-and-alternatives/anoncreds/revocation-registry-definition.md)
  * [Revocation Status Lists](advanced-features-and-alternatives/anoncreds/revocation-status-list.md)
* [🤓 Direct interaction with ledger code](advanced/README.md)
* [🛰 cheqd Cosmos CLI for identity](advanced/cheqd-node-cli/README.md)
  * [Creating a DID with cheqd Cosmos CLI](advanced/cheqd-node-cli/create-did.md)
  * [Updating existing DIDs with cheqd Cosmos CLI](advanced/cheqd-node-cli/update-did.md)
  * [Deactivating existing DIDs with cheqd Cosmos CLI](advanced/cheqd-node-cli/deactivate-did.md)
  * [Querying DIDs with cheqd Cosmos CLI](advanced/cheqd-node-cli/query-did.md)
  * [Creating a DID-Linked Resource using cheqd Cosmos CLI](advanced/cheqd-node-cli/create-resource.md)
  * [Adding a new Resource version](advanced/cheqd-node-cli/add-more-resources.md)
* [🛅 Demo Wallet for identity setup](advanced-features-and-alternatives/demo-wallet.md)
* [⚒ VDR Tools CLI with cheqd (deprecated)](advanced/vdr-tools/README.md)
* [💳 Wallets](advanced-features-and-alternatives/wallets/README.md)
  * [Setup Keplr wallet](advanced-features-and-alternatives/wallets/keplr-setup.md)

## ⚖️ Legal

* [License](LICENSE.md)
* [Code of Conduct](CODE\_OF\_CONDUCT.md)
* [Security Policy](SECURITY.md)

## 🆘 Support

* [System Status](https://status.cheqd.net)
* [Slack Channel](http://cheqd.link/join-cheqd-slack)
* [Discord](http://cheqd.link/discord-github)
