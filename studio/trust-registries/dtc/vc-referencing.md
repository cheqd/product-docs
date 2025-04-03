# Referencing Trust Registry within a Verifiable Credential

Trust Registries are referenced within **Policies** in the Verifiable Credential body. This enables Relying Parties to traverse the trust chain and verify that the **issuer**, accrediting entity (**TAO**) and Root of Trust (**rTAO**) are all legitimate entities.

Within the body of the Verifiable Credential, trusted issuers will need to configure the `termsOfUse` section to reference DIDs or DID URLs of trust registry entries, for example:

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
    "rootAuthorisation": "did:cheqd:testnet:c2f18b6b-32e2-48d1-a5a8-5f5d2d9798f0/resources/58c01595-f884-4a3b-add4-8c691e16b8ee"
  }
}
```

For Verifiable Credentials that are issued (not accreditations), the following sections of the `termsOfUse` section MUST be present:

| Parameter             | Input                   | Required |
| --------------------- | ----------------------- | -------- |
| `type`                | AccreditationPolicy[^1] | Yes      |
| `parentAccreditation` | A valid DID URL         | Yes      |
| `rootAuthorisation`   | A valid DID URL         | Yes      |

Through parsing through this credential, a relying party can check the accreditation of the issuer, the  "parent" accreditation. This accreditation will include its own Policy pointing up the trust chain all the way to a Root of Trust.

Using a protocol like TRAIN, a relying party can take the verifiable credential as an input and check whether the issuer is accredited to issue the type of credential, all the way back to a Root of Trust, and according to a particular governance framework.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>TRAIN on cheqd</strong></mark></td><td>Deploy a TRAIN to verify Decentralized Trust Chains back to a Root of Trust DID and DNS Record.</td><td><strong>Contact us</strong></td><td><a href="https://cheqd.io/contact/">https://cheqd.io/contact/</a></td><td><a href="../../../.gitbook/assets/TRAIN_for_website.png">TRAIN_for_website.png</a></td></tr></tbody></table>

[^1]: 
