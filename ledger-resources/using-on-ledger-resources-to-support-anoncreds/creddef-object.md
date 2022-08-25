# CredDef Object

## Overview

In AnonCreds, Credential Definitions are used to specify the following information all in one place, to create an immutable record of:

1. The DID of the credential issuer
2. The schema the issued credentials will be based upon
3. The public/private key pairs that will be used to sign the claims within an issued credential
4. A cryptographic secret, embedded within the CredDef Object content, creating an uncorrelatable 'link secret' between the issuer and holder
5. Information necessary for the revocation of credentials, if revocation is to be enabled by the Issuer for this type of credential (Optional).



Prior to creating a [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def), the Issuer must get an instance of the [SCHEMA](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema) upon which the [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def) will be created, including the identifier for the [SCHEMA](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema) in the form of a SchemaId. If the Issuer is also the [SCHEMA Publisher](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema-publisher), they will already have the [SchemaId](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema). If not, the Issuer must request that information from the VDR on which the [SCHEMA](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema) is published. Hyperledger Indy requires that the [SCHEMA](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema) and [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def) must be on the same ledger instance.





```json
{
"AnonCredsCredDef: {
  "ref": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",,
  "signature_type": "CL",
  "tag": "degreeCredDef"
  },
"AnonCredsObjectMetadata": {
  "data": {
    "primary": {
      "n": "779...397",
      "r": {
            "attr_names": [
                "birthlocation": "294...298",
                "facephoto": "124...240",
                "expiry_date": "057...124",
                "citizenship": "966...235",
                "master_secret": "562...831",
                "name": "574...152",
                "birthdate": "235...113",
                "firstname": "103...443",
                "uuid": "459...559"
          },
      },
      "rctxt": "774...977",
      "s": "750..893",
      "z": "632...005"
      }
   "issuerDid": "did:cheqd:mainnet:6b245956-331f-45e4-bf98-9032cf15dbf4",      
   "objectUri": "did:cheqd:mainnet:6b245956-331f-45e4-bf98-9032cf15dbf4/resources/c3bf9bf3-b2de-434b-8689-ad363b62641c"
  },  
}
```

The [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def) is a JSON structure that is generated using cryptographic primitives (described below) given the following inputs.

* A [SCHEMA](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema) for the credential type.
* A `tag`, an arbitrary string defined by the Issuer, enabling an Issuer to create multiple [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def)s for the same [SCHEMA](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema).
* An optional flag `support_revocation` (default `false`) which if true generates some additional data in the [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def) to enable credential revocation. The additional data generated when this flag is `true` is covered in the [next section](https://anoncreds-wg.github.io/anoncreds-spec/#issuer-create-and-publish-revocation-registry-object) of this document.



### CredDef Object ID

```
<issuer DID>:<object type>:<signature_type>:<SCHEMA TXN_ID>:tag>
```

The elements of the identifier, separated by `:`'s are:

* `issuer DID`: The DID of the Issuer, the issuer of the [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def).
* `object type`: The type of object. `3` is used for [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def)s.
* `signature_type`: The `signature_type` item from the [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def).
* `SCHEMA TXN_ID`: The `ref` item from the [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def)
* `tag`: The `tag` item from the [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def).
