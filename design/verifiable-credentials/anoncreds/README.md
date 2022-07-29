# AnonCreds

{% hint style="info" %}
_Much of the work in this page is influenced, iterated or directly replicated from_ [_Kaliya Young's_](https://identitywoman.net/about-kaliya/bio/) _excellent document on_ [_Verifiable Credential Flavors Explained_](https://www.lfph.io/wp-content/uploads/2021/02/Verifiable-Credentials-Flavors-Explained.pdf)_. We intend to give appropriate credit to this work, as expressed under a_ [_Creative Commons Attribution 4.0 International License_](https://creativecommons.org/licenses/by/4.0/)_._
{% endhint %}

## What are AnonCreds?

AnonCreds are a **type** or **flavour** of Verifiable Credentials that were originally developed by the company [Evernym](https://www.evernym.com/), and were subsequently adopted by the [Sovrin Foundation](https://sovrin.org/) as part of the 'Indy' codebase that was contributed to [Hyperledger](https://www.hyperledger.org/), a [Linux Foundation](https://linuxfoundation.org/) project. This codebase was forked into the [Hyperledger Indy](https://www.hyperledger.org/use/hyperledger-indy) project for ledger code, [Hyperledger Ursa](https://www.hyperledger.org/use/ursa) for cryptographic libraries, and the ledger-agnostic Hyperledger Aries codebase for agent, wallet, and credentialing code.

AnonCreds are now far more widely adopted, through organisations such as the [Government of British Colombia](https://digital.gov.bc.ca/digital-trust/projects-and-initiatives/credentials-for-people/), [IDunion](https://idunion.org/projekt/?lang=en) and the [IATA Travel Pass](https://www.evernym.com/travelpass/).&#x20;

### How are AnonCreds different to other Credential types?

AnonCreds were designed to provide additional privacy benefits, compared with JSON and JSON-LD based Verifiable Credentials. There are a series of differences that can be summarised as follows:

* **Camenisch-Lysyanskaya (ZKP CL-Signatures)**: a Credential format which is not defined in the W3C Verifiable Credential Data Model. Rather it is a bespoke data expression format coupled with a particular digital signature algorithm.
* **Link Secrets:** A mechanism to link ZKP-CL credentials to DIDs, by including a DID for the holder in the credential’s schema. Holders can prove that Credentials are not tampered with and that they are authentic, since holders are the only party that knows the Link Secrets made at the point of issuance.
* **Schemas:** The schemas are defined in a document and they are written to an Indy ledger where they can’t be changed. To update a schema, a new version must be created and written to the ledger for future use. This schema is fetched from the ledger by verifiers when calculating the verification.
* **Credential definitions:** Each issuer of a Verifiable Credential must post a credential definition that says to the world, "I intend to publish credentials from schema X, signed using keys Y\[0]...Y\[N], and with revocation strategy Z". This helps verifiers prove that the Credential has not been tampered with.
* **Predicate proofs:** Through the use of link secrets and Indy schemas, it is possible to present an assertion, such as "date\_of\_birth is more than 18 years ago)", without revealing any additional information.
* **Privacy preserving revocation:** Using cryptographic accumulators and 'tails files', revocation statuses can be queried without creating a chain of correlation through which a user's privacy can be compromised.

The ZKP-CL processing model requires that the credential’s schema is defined and posted to an Indy ledger. The credential issuer follows the pre-defined schema. They leverage the CL Signature suite to sign each statement in the credential. The credential is anchored to a link secret that is known only to the holder (stored in the holder’s software), and when the holder is issued a credential, it packages up a cryptographic commitment to the link secret within another long number that the issuer uses for the credential ID.&#x20;

A metaphor for this is that of a digital watermark; the link secret is like a stamp that is used to create a watermark. The stamp used to create the watermark is NOT packaged up or put in the credential in any form; all that is in the credential is very-hard-to-fake evidence that the holder possesses the stamp and is capable of generating such watermarks. Unlike non-ZKP methods, zero-knowledge methods generally do not share a correlatable identifier (such as a persistent or public DID) and also do not reveal actual signatures. Instead, they only reveal a cryptographic proof of a valid signature. This means the holder of the credential cannot be correlated just by presenting a proof of the credential (this is the primary privacy problem with public/private key cryptography that ZKP-CL Signatures were invented to solve over a decade ago).

All other non-ZKP Signature formats require a resolvable DID—which is a correlatable globally unique identifier—and also produce the same signature on all equivalent presentations, which is a second fully correlatable globally unique identifier

### Why are AnonCreds a contentious issue?

AnonCreds have been the subject of heavy debate within the decentralized identity community for the following reasons:

* **Interoperability:** AnonCreds are **** intrinsically tied to Hyperledger Indy, and largely to Hyperledger Aries. Historically, they cannot be written to other Layer 1 Utilities, nor are they compliant with the W3C Verifiable Credential data model, given their core architectural differences. As such, this shoehorns adopters into a particular tech stack, which although open source, is largely reliant on the Indy/Aries community for updates, since there is no formal Standard.
* [AnonCreds v1](https://github.com/AnonCreds-WG/anoncreds-spec) is being proposed as a Standard and is in the process of being taken to the [Internet Engineering Task Force (IETF)](https://www.ietf.org/).
* **Scalability:** The use of ZKP-CL signatures, whilst certainly containing benefits from a privacy perspective, are very large files which can lead to inefficiencies when used at scale in production environments.
* Hyperledger Indy has performance and scalability issues when it comes to running more than 25 nodes in terms of its throughput. Its method of revocation also generates very large files, which is very slow to compute in a low-latency environment.&#x20;

Nonetheless, many proponents of SSI contend that AnonCreds are still the most production-ready Credential types at this present instance. Certainly, AnonCreds are the only off-the-shelf Credential type that can provide privacy-preserving functionality such as predicate proofs, which is why, for example, Governments such as [BC-Gov](https://digital.gov.bc.ca/digital-trust/projects-and-initiatives/credentials-for-people/) are large proponents of AnonCreds.

## AnonCreds support

cheqd intends to directly support AnonCreds in a W3C compliant format.

### Why is AnonCred support important?

Whilst AnonCreds are not the end-game Credential type, they are currently in a functional state. Other Credential types, such as JSON-LD with BBS+ signatures, can provide a lot of equivalent functionality, but are currently not production ready. Therefore, in this interim period, it is important for cheqd to provide support for AnonCreds in order to enable partners with existing clients to use cheqd and existing Indy ledgers without any hiccups.

### What does AnonCred support look like?

Our aim is to support the functionality enabled by [identity-domain transactions in by Hyperledger Indy](https://github.com/hyperledger/indy-node/blob/master/docs/source/transactions.md) into `cheqd-node`. This will partly enable the goal of allowing use cases of existing SSI networks on Hyperledger Indy to be supported by the cheqd network.&#x20;

Importantly, we want to make sure that this work is done in a manner which remains W3C compliant and interoperable.

### Compatibility

One of the aforementioned ways AnonCreds differ from JSON and JSON-LD Credentials is the way Schemas are stored. With JSON and JSON-LD Credentials, schemas are generally stored off-ledger. A common place for schemas to be stored is on [schema.org](https://schema.org/). This enables the schema to be updated or changed far more easily. However, [schema.org](https://schema.org/) is a centralised web domain, which presents a single point of failure for the entire SSI ecosystem, if schemas cannot be properly fetched. For this reason, cheqd is reworking the way that schemas are stored, within the parameters of DID Core and the Verifiable Credential Data Model.

### **How does cheqd support CL-Schemas?**

The way cheqd facilitates Resources on-ledger is **crucial to understanding** how AnonCreds are supported.&#x20;

{% hint style="info" %}
Please take time to read the section on Resources to understand this approach
{% endhint %}

{% content-ref url="../../resources/" %}
[resources](../../resources/)
{% endcontent-ref %}

**How does this make it compatible?**

Through linking on-ledger CL-Schemas to DID Documents, and tying them to Issuer DID Documents, we replicate the functionality of Link Secrets.&#x20;

We want to avoid making a new Credential standard, and as such, we want to use the existing W3C VC spec to get as close to AnonCreds as possible.&#x20;

The table below summarises where we are at:

| CL-Signatures                 | Supported | Supported            |                                                                                                                                                                                                         |
| ----------------------------- | --------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Link secrets                  | Supported | Indirectly supported | Not supported but not necessarily needed as tamper-proofing can carried out through DID Document Verification Relationships                                                                             |
| CL-Schemas on-ledger          | Supported | Supported            | CL-Schemas on ledger linked to a DID Document.                                                                                                                                                          |
| Credential definition         | Supported | Supported            | `Get` CredDef can be facilitated through the use of the DID Docs and Schemas as DIDs compiling all the necessary components of a CredDef                                                                |
| Predicate proofs              | Supported | In progress          | We need to look into this in more detail. We may be able to use the existing 1-1 encoding Credentials to facilitate this with BBS+ (would be pretty cool and would align with AnonCreds' plan for v2.0) |
| Privacy preserving revocation | Supported | In progress          | Pending cheqd's revocation method. Not required for basic AnonCreds. But yes, this would not be **full** AnonCreds until the revocation registry is ready.                                              |

### **How does cheqd support CredDefs?**

{% content-ref url="credential-definitions.md" %}
[credential-definitions.md](credential-definitions.md)
{% endcontent-ref %}

### **How does cheqd support privacy preserving revocation?**

**\<todo>**
