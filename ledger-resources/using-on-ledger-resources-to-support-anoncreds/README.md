# 🔒 Using on-ledger resources to support AnonCreds

## AnonCreds support

cheqd intends to directly support AnonCreds in a W3C compliant format using its [on-ledger resource module](../../resources/).

### Why is AnonCreds support important?

Whilst AnonCreds are not the end-game Credential type, they are currently in a functional state. Other Credential types, such as JSON-LD with BBS+ signatures, can provide a lot of equivalent functionality, but are currently not production ready. Therefore, in this interim period, it is important for cheqd to provide support for AnonCreds in order to enable partners with existing clients to use cheqd and existing Indy ledgers without any hiccups.

### What does AnonCreds support look like?

Our aim is to support the functionality enabled by [identity-domain transactions in by Hyperledger Indy](https://github.com/hyperledger/indy-node/blob/master/docs/source/transactions.md) into `cheqd-node`. This will reach the goal of allowing use cases of existing SSI networks on Hyperledger Indy to be supported by the cheqd network.

Importantly, we want to make sure that this work is done in a manner which remains W3C compliant and interoperable.

### Compatibility

There are multiple specific AnonCreds transaction types that cheqd needs to support. This documentation will provide guidance and instructions on how equivalent AnonCreds formats can be replicated using cheqd resources, for:

* Schema Objects
* Credential Definition Objects
* Revocation Registry Objects
* Revocation Registry Entries

Through creating compatible formats for the above, cheqd will theoretically be able to support AnonCreds issuance data flows, including

* Credential offer
* Credential request
