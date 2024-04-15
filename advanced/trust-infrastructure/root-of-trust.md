# Root of Trust

## Traditional Root of Trust

For existing trust frameworks such as eIDAS, organisations need to establish a root of trust using [X.509 certificates](https://www.sectigo.com/resource-library/what-is-x509-certificate) in traditional "[Trusted List](https://digital-strategy.ec.europa.eu/en/policies/eu-trusted-lists)" infrastructure. These certificates establish that an organisation is authorised to provide a service under a particular jurisdiction.&#x20;

Article 22 of the eIDAS Regulation obliges Member States to establish, maintain and publish trusted lists. These lists should include information related to the qualified trust service providers for which they are responsible, and information related to the qualified trust services provided by them. The lists are to be published in a secured manner, electronically signed or sealed in a format suitable for automated processing.

Standard information in an X509 certificate includes:

* **Version** — The version of X.509 that applies to the certificate.
* **Serial number** — Serial number assigned by certificate authority to distinguish one certificate from other certificates.
* **Algorithm information** — The hashing algorithm used by the CA to sign the certificate (SHA-2 in almost all cases).
* **Issuer distinguished name** — The name of the entity issuing the certificate (usually a certificate authority)
* **Validity period of the certificate** — The period during which certificate is valid to use.
* **Subject distinguished name** — The name of the identity the certificate is issued to (individual, organization, domain name, etc.)
* **Subject public key information** — The public key of the certificate

As such, one method of establishing a root of trust is associating a DID on cheqd with an existing X.509 certificate. This concept has been written on recently, with there being various approaches to achieving this.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Combination of x509 and DID/VC for inheritance properties of trust in digital identities</strong></mark></td><td>Paper written by Carsten Stocker, Paul Bastian and Steffen Schwalm on the different methods for associating DIDs with X.509 certificates</td><td><a href="https://dl.gi.de/server/api/core/bitstreams/a733c999-351a-43ad-b8ec-36b696947cbf/content">https://dl.gi.de/server/api/core/bitstreams/a733c999-351a-43ad-b8ec-36b696947cbf/content</a></td></tr></tbody></table>

As a first iteration of trust infrastructure on cheqd, we suggest that:

1. `did:cheqd` DIDs can be derived from the same public/private key pair as existing X.509 certificates
2. `did:cheqd` DIDs can reference X.509 certificates using a serviceEndpoint
3. X.509 certificates can reference `did:cheqd` DIDs using the "Subject Alternative Name" field within the X.509 certificate.

This will enable Root TAOs to create a reciprocal root of trust across European Trusted Lists for eIDAS compliance, and equally on cheqd.&#x20;

## Decentralised Root of Trust

For ecosystems that do not require a "traditional" root of trust, we can leverage the cheqd network itself to create reputable, "weighted" roots of trust.

For this model, we suggest that "Root TAOs" [set up a cheqd Validator](https://docs.cheqd.io/node/getting-started/setup-and-configure) on the cheqd Network and include their DID in the "description" field of their Validator. This will tie the reputation and weight of the Validator with the DID itself.

Then, when traversing the trust chain and resolving to the Root of Trust, the validating application can apply logic to validate the "reputation" of the Root TAO, based on the voting power of the Validator within the active set.&#x20;
