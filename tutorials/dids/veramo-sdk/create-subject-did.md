# Create a new subject DID with did:key


## Tutorial for creating a new subject DID with did:key

### Overview

This tutorial offers step-by-step guidance on how to create a subject DID to be used for the holder that will receive a Verifiable Credentials, using the Veramo SDK for cheqd.

We chose to use [`did:key`](https://github.com/w3c-ccg/did-method-key) for the holder because the [`did:key`](https://w3c-ccg.github.io/did-method-key/) specification has a lot synergy with cheqd did method, We chose to use [`did:key`](https://github.com/w3c-ccg/did-method-key) for the holder because the [`did:key`](https://w3c-ccg.github.io/did-method-key/) specification has a lot synergy with cheqd did method, [`did:cheqd`](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method)

Using [`did:key`](https://github.com/w3c-ccg/did-method-key) also keeps the ability to point to a non-cheqd ledger, encouraging greater interoperability. It is also easily managable by a client agent as a full ledger backup for the writing of a DID / DID Doc is not required, thereby making it easily reproducable in a client environment.

Create a subject [`did:key`](https://github.com/w3c-ccg/did-method-key) DID   can be achieved through both the Veramo CLI, or directly through an application, setup to read and write to the cheqd ledger, using the Veramo SDK for cheqd.

### Pre-requisites

For full information on the architecture, setup and cofig, check [`did-provider-cheqd`]().

>Note: Ensure you have saved the [`agent.yml`](https://raw.githubusercontent.com/cheqd/did-provider-cheqd/main/agent.yml) file in your local project directory.

### Steps

#### Step 1

Create a new subject did, used for the recipient of the Verifiable Credential and Verifiable Presentation using:

```bash
veramo did create
```

#### Step 2

Select identifier provider.

Select `did:key`

#### Step 3

Select key management system (KMS). The [`agent.yml`](https://github.com/cheqd/did-provider-cheqd/blob/main/agent.yml) file used for the Veramo SDK for cheqd has a preloaded local key management system which is bootstrapped along with the CLI.

This provides a sqlite file which is dumped to your specified root directory.

#### Step 4

Enter alias.

This is the nickname for your did, which will be managed by the key management system (KMS). Naming this to something more memorable will help when retrieving dids from your KMS.

For example, enter `'John government ID`’.

Once you have entered this, you will receive a response in a table, e.g:

| provider                  | alias                  | did |   |
| :--------------------- | :----------------------- | :----------------- | :------------------------------ |
| did:key     | John's Gov ID  | did:key:z6MkjxdqDUWoudesywsGYbzGozUeVYiYVArdoqPcMV6m6MG4                                |

Your `did:key` has now been created.
