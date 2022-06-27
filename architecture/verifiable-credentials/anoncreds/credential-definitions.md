# Credential Definitions

\<to be expanded on>

## Overview

\<todo>

### What goes into a CredDef?&#x20;

[https://indyscan.io/tx/SOVRIN\_MAINNET/domain/56496](https://indyscan.io/tx/SOVRIN\_MAINNET/domain/56496)

* **Schema name**
* **Schema version**
* **Schema ID**
* **Schema author DID**
* **Schema seqNo**
* **Schema create time**
* **Signature type**
* **Type**
* **Typename**
* **Tag**
* **TransactionID**
* **Transaction Time**
* **Attributes**

Although cheqd won't directly store CredDefs on ledger, it will store everything required to compile a CredDefs within the:

1. DID Document services section
2. Schemas on ledger

Therefore, a `get` CredDef request will assemble the required components in real time, to provide the verifier a CredDef if necessary. This provides direct compatibility and equivalency with the actual AnonCreds standard.
