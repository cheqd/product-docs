# 🔒 Using on-ledger resources to support AnonCreds

## cheqd AnonCreds Object Method

cheqd intends to directly support AnonCreds using its [on-ledger resource module](../../resources/) in an AnonCreds Object Method. With its resource module, cheqd will identify each on-ledger resource with a [DID Core](https://www.w3.org/TR/did-core/) compliant [DID URL](https://www.w3.org/TR/did-core/). This DID URL will be able to be dereferenced in order to fetch the resource and associated metadata

### Why is AnonCreds support important?

Whilst AnonCreds are only one _flavour_ of Verifiable Credentials, they are currently in a functional state and are heavily used by cheqd partners. Other Credential types, such as JSON-LD with BBS+ signatures, can provide a lot of equivalent functionality, but are currently not production ready.&#x20;

Therefore, it is important for cheqd to provide support for AnonCreds in order to enable partners with existing clients using AnonCreds to use cheqd and existing Indy ledgers without additional technical challenges.

### What does AnonCreds support look like?

Our aim is to support the functionality enabled by [identity-domain transactions in by Hyperledger Indy](https://github.com/hyperledger/indy-node/blob/master/docs/source/transactions.md) into `cheqd-node`. This will reach the goal of allowing use cases of existing SSI networks on Hyperledger Indy to be supported by the cheqd network.

Importantly, we want to make sure that this work is done in a manner which remains W3C compliant and interoperable.

### Compatibility

There are multiple specific AnonCreds transaction types that cheqd needs to support. This documentation will provide guidance and instructions on how equivalent AnonCreds formats can be replicated using cheqd resources, for:

* [Schema Objects](schema-object.md)
* [Credential Definition Objects](creddef-object.md)
* [Revocation Registry Definition Objects](revocation-registry-definition-object.md)
* [Revocation Registry Entry Objects](revocation-registry-entry-object.md)

Through creating compatible formats for the above, cheqd will theoretically be able to support AnonCreds issuance data flows, via existing SDKs such as Hyperledger Aries Libraries.&#x20;
