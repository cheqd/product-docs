# Referencing Trust Registry within a Verifiable Credential

Trust Registries are referenced within **Accreditaiton Policies** in the Verifiable Credential body. This enables Relying Parties to traverse the trust chain and verify that the **issuer**, accrediting entity (**TAO**) and Root of Trust (**rTAO**) are all legitimate entities.

Within the body of the Verifiable Credential, issuers will need to configure the `termsOfUse` section to reference DIDs or DID URLs of trust registry entries, for example:

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://www.w3.org/ns/credentials/examples/v2"
  ],
  "id": "http://university.example/credentials/3732",
  "type": ["VerifiableCredential", "ExampleDegreeCredential"],
  "issuer": "did:cheqd:testnet:c2f18b6b-32e2-48d1-a5a8-5f5d2d9798f0",
  "validFrom": "2010-01-01T00:00:00Z",
  "credentialSubject": {
    "id": "did:cheqd:testnet:b4902745-5b5b-423e-820a-0773b033f2b9",
    "degree": {
      "type": "ExampleBachelorDegree",
      "name": "Bachelor of Science and Arts"
  "termsOfUse": {
    "type": "AccreditationPolicy",
    "parentAccreditation": "did:cheqd:testnet:c2f18b6b-32e2-48d1-a5a8-5f5d2d9798f0/resources/58c01595-f884-4a3b-add4-8c691e16b8ee",
    "rootAuthorisation": "did:cheqd:testnet:c2f18b6b-32e2-48d1-a5a8-5f5d2d9798f0/resources/58c01595-f884-4a3b-add4-8c691e16b8ee",
    "trustFramework": "cheqd Governance Framework",
    "trustFrameworkId": "https://learn.cheqd.io/governance/start"
  }
}
```
