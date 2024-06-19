# Understanding Trust Infrastructure on cheqd

## Introduction[​](https://hub.ebsi.eu/vc-framework/trust-model/issuer-trust-model-v3#introduction) <a href="#introduction" id="introduction"></a>

Verifiable Credentials (VCs) are, in most cases, issued by legal entities. The purpose of Verifiable Credentials is to provide the Relying Party that receives the credentials a level of assurance that the attributes and claims within the credential are legitimate. However, it is **not currently possible to determine whether a legal entity issuing a credential is in fact the entity they claim to be**, and not, a fraudulent misrepresentation of that legal entity. This is the challenge that Trust Infrastructure and Trust Registries are positioned to solve.

{% hint style="info" %}
Note: This **Trust Registry challenge is a significant problem** for the digital credential industry, and often inhibits the technology reaching a production stage of readiness.
{% endhint %}

At present, legal entities that issue credentials have no mechanism to establish that they are trustworthy; thus, Relying Parties may not recognise the DIDs signing the Verifiable Credentials they receive. To fully establish trust, Relying Parties need to know _**who**_ issued the VCs, whether the _**issuer is recognised**_ **as trusted** within a particular governance framework, and _**who accredited the issuer for the purpose of issuing the credential**_.&#x20;

To solve this industry-wide challenge, cheqd introduces a **Verifiable Trust Infrastructure**, that directly complements the model [created by EBSI](https://hub.ebsi.eu/vc-framework/trust-model/issuer-trust-model-v3). Within cheqd's Trust Infrastructure, users can create hierarchical chains of trust "**Trust Chains"** that together encapsulate a "**Trust Registry"** for a given ecosystem.

The Trust Infrastructure Model also includes **permissions and policies** set via "**Verifiable Accreditations**" and an overall "**Governance Framework**". Herein, permissions govern the scope of , while policies are used to define who made the accreditation; which Trust Framework is followed; and, the legal basis of the credential.&#x20;

cheqd Trust Infrastructure users **make the whole Verifiable Trust Model publicly available by registering it as a collection of** [**DID-Linked Resources**](../../../architecture/adr-list/adr-002-did-linked-resources.md) on cheqd. cheqd's Trust Infrastructure therefore enables verifiers to automatically resolve and establish trust in hierarchies of trust without needing to know each organisation directly, using industry-standard resolution mechanisms defined in the W3C DID-Core and the DID Resolution Spec.

## Glossary[​](https://hub.ebsi.eu/vc-framework/trust-model/issuer-trust-model-v3#glossary) <a href="#glossary" id="glossary"></a>

There are many terms used within this guide, and as such, familiarise yourself or refer back to the concepts within the glossary below:

| Abbreviation | Term                                    | Description                                                                                                                                   |
| ------------ | --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| DID          | Decentralised Identifier                | Legal entity identifier for Trust Registry, cannot be natural person in context of Trust Infrastructure                                       |
|              | Governance Authority                    | The legal entity or consortia responsible for writing the Governance Framework. In many instances the Governance Authority is also a Root TAO |
| GF           | Governance Framework                    | A policy document outlining the purpose, roles, scopes and permissions for a given ecosystem using the Trust Infrastructure.                  |
| Root TAO     | Root Trusted Accreditation Organization | Legal entity governing the whole trust chain                                                                                                  |
| TAO          | Trusted Accreditation Organization      | Legal entity governing a trust chain segment                                                                                                  |
| -            | Trust Chain                             | Hierarchy of Verifiable Accreditations. Multiple Trust Chains may comprise a Trust Registry.                                                  |
| TI           | Trusted Issuer                          | Legal entity participating in a trust chain as an issuer                                                                                      |
| -            | Trust Infrastructure                    | The overall set of technical and governance components to establish end-to-end trust.                                                         |
| -            | Verifiable Accreditation                | Type of on-ledger Verifiable Credential that is specifically used for establishing governance permissions and policies                        |
| -            | Verifiable Trust Model                  | Permissions with policies to either accredit, or to attest                                                                                    |

## Trust Infrastructure Roles and their Permissions <a href="#trust-model-roles-and-their-permissions" id="trust-model-roles-and-their-permissions"></a>

Depending on their accreditations and authorisations, legal entities can play the following roles:

* **Root Trusted Accreditation Organisation (Root TAO)**
* **Trusted Accreditation Organisation (TAO)**
* **Trusted Issuer (TI)**

A Trust Chain should contain all three roles, even if one single DID would represent all three roles. The roles must be RTAO, TAO, and TI, where only TI may issue domain-specific Verifiable Credentials.

### **Root Trusted Accreditation Organisation (RTAO)**

The **Root TAO** is the owner of a **Trust Chain**, responsible for the **governance of the whole Trust Chain**. Root TAOs may:

* accredit itself to govern or issue domain-specific Verifiable Credentials
* accredit TAOs to govern a segment of the Trust Chain
* accredit a Trusted Issuer to issue domain-specific Verifiable Credentials
* revoke an accreditation from a legal entity that is participating in the Trust Chain

The RTAO permission is defined by `VerifiableAuthorisationForTrustChain`, and the policies are contained in `termsOfUse` as `TrustFrameworkPolicy`.

### **Trusted Accreditation Organisation (TAO)**[**​**](https://hub.ebsi.eu/vc-framework/trust-model/issuer-trust-model-v4#trusted-accreditation-organisation-tao)

A TAO governs an accredited segment on behalf of the RTAO. It may:

* accredit itself to issue domain-specific Verifiable Credentials
* accredit another TAO to govern a segment of the Trust Chain
* accredit a Trusted Issuer to issue domain-specific Verifiable Credentials
* revoke accreditation from a legal entity that was accredited by the TAO

The TAO permission is defined by `VerifiableAccreditationToAccredit`, and the policies are contained in `termsOfUse` as `AccreditationPolicy`.

### **Trusted Issuer (TI)**[**​**](https://hub.ebsi.eu/vc-framework/trust-model/issuer-trust-model-v4#trusted-issuer-ti)

A Trusted Issuer represents the Issuer in a Trust Chain. It may issue domain-specific Verifiable Credential types defined by the received accreditation.&#x20;

{% hint style="info" %}
Note that issuers may issue Verifiable Credentials outside the Trust Chain, but these are not associated or recognised by a Root TAO and therefore contain no weight within the Trust Chain's governance framework.
{% endhint %}

The TI permission is defined by `VerifiableAccreditationToAttest`, and the policies are contained in `termsOfUse` as `AccreditationPolicy`. When the Trusted Issuer is using their accreditation to issue a domain-specific VC, the issued domain VC must contain a `termsOfUse` property with `AttestationPolicy` type, which links to the Trusted Issuer's accreditation and into Root TAO's accreditation, where both are located in TIR.

## Policies Overview[​](https://hub.ebsi.eu/vc-framework/trust-model/issuer-trust-model-v4#policies-overview) <a href="#policies-overview" id="policies-overview"></a>

The Governance Framework Policy is a document, written by a Governance Authority,  that defines requirements that must be met for the Trust Ecosystem. These requirements may include security, legal, operational, or functional requirements and may relate to regulation, directives, national policy, or similar documents.

All Trust Model policies are located in the `termsOfUse` property of the corresponding `VerifiableTrustModel` credential that contains the permissions related to the policy.

<figure><img src="../../../.gitbook/assets/image.png" alt="" width="375"><figcaption></figcaption></figure>

### Concepts[​](https://hub.ebsi.eu/vc-framework/trust-model/issuer-trust-model-v3#concepts) <a href="#glossary" id="glossary"></a>

A trust chain creates a common and highly accessible point of trust for the given trust domain. Each Verifier can choose to trust the Root TAO and verify the accreditation chain that is referenced from the issued Verifiable Credential. All Verifiable Accreditations are public information and stored as DID-Linked Resources on the cheqd network.

### &#x20;<a href="#trust-model-roles-and-their-permissions" id="trust-model-roles-and-their-permissions"></a>

#### Accreditations[​](https://hub.ebsi.eu/vc-framework/trust-model/issuer-trust-model-v4#accreditations) <a href="#accreditations" id="accreditations"></a>

Accreditations are certifications of being qualified to accredit or attest. Accreditations are attribute-driven and are always restricted to domain-specific credential types. These restrictions cannot be extended. For example, if a legal entity is accredited to accredit Issuers of diploma VCs, they may only pass this or a subset downstream of the hierarchy. Depending on the accreditation, the accredited legal entity may govern (accredit) or issue (attest), but always within the Trust Model and the accredited boundaries.

#### Attestations[​](https://hub.ebsi.eu/vc-framework/trust-model/issuer-trust-model-v4#attestations) <a href="#attestations" id="attestations"></a>

All Verifiable Credentials are attestations of something. Any issuer may issue non-accredited attestation (default), while accredited Trusted Issuers may issue domain-specific VCs with the accreditation, by attaching the `AttestationPolicy` into `termsOfUse`.

End Users (legal entities or natural persons) can accumulate multiple Verifiable Credentials from one or many Trust Models.

### Hierarchy example <a href="#hierarchy-examples" id="hierarchy-examples"></a>

The following diagram show how a Root TAO accredits two TAOs lower in the hierarchy&#x20;

\<todo>

### Permissionless Trust Framework <a href="#policies-overview" id="policies-overview"></a>

Unlike the [EBSI Verifiable Trust Model](https://hub.ebsi.eu/get-started/design/trust-chain) which requires Root TAOs to obtain authorisation from the EBSI office to write to the Trust Registry, cheqd employs a permisionless model, allowing any organisation to set-up a Root TAO DID and accredit other organisations.&#x20;
