# ✅ Verifiable Credentials

A Verifiable Credential (VC) is a tamper-evident credential that has authorship that can be cryptographically verified. Verifiable credentials can be used to build [verifiable presentations](https://www.w3.org/TR/vc-data-model/#dfn-verifiable-presentations), which can also be cryptographically verified. The [claims](https://www.w3.org/TR/vc-data-model/#dfn-claims) in a credential can be about different [subjects](https://www.w3.org/TR/vc-data-model/#dfn-subjects).

If you want to learn more about what [Verifiable Credentials are, please go over to our learning site here.](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc)

## Verifiable Credential types

There are multiple different types of Verifiable Credentials, each with different compositions and syntaxes. The problem with different VCs is that they do not always interoperate with each other, and often need completely separate software to process them.

For this reason, cheqd intends to support all mainstream types of Verifiable Credential, in order to best support its partners building on the network:

### JSON/JWT-based Verifiable Credentials

[JSON/JWT-VCs](json-jwt-credentials.md) are currently possible with live demos available in [our identity demo wallet](wallet.md)

### JSON-LD Verifiable Credentials

[JSON-LD Credentials](json-ld-credentials.md) can be supported in theory, with compatible SDKs.

### AnonCreds Credentials

[AnonCreds Credentials can be supported on cheqd](../building-decentralized-identity-apps/verifiable-credentials/anoncreds.md) with our [Resources on-ledger module](../resources/).

> **Learn more about our client app SDK roadmap on our product site**
>
> Our [Product Update & Roadmaps site](https://product.cheqd.io) has the latest updates for business & developer teams looking to use our Product SDKs and integrate functionality into their own applications
