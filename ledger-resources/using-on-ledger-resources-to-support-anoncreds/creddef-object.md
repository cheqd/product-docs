# CredDef Object

## Overview

In AnonCreds, the [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def) and [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def) identifier include the following elements.

* A link to the Issuer of the credentials via the DID used to publish the [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def).
* A link to the [SCHEMA](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema) upon which the [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def) is based (the credential type).
* A set of public/private key pairs, one per attribute (claim) in the credential. The private keys will later be used to sign the claims when credentials to be issued are created.
* Other information necessary for the cryptographic signing of credentials.
* Information necessary for the revocation of credentials, if revocation is to be enabled by the Issuer for this type of credential.

Prior to creating a [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def), the Issuer must get an instance of the [SCHEMA](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema) upon which the [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def) will be created, including the identifier for the [SCHEMA](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema) in the form of a SchemaId. If the Issuer is also the [SCHEMA Publisher](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema-publisher), they will already have the [SchemaId](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema). If not, the Issuer must request that information from the VDR on which the [SCHEMA](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema) is published. Hyperledger Indy requires that the [SCHEMA](https://anoncreds-wg.github.io/anoncreds-spec/#term:schema) and [CRED\_DEF](https://anoncreds-wg.github.io/anoncreds-spec/#term:cred\_def) must be on the same ledger instance.

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
