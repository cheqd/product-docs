# Create an off-ledger DID for credential subjects with Veramo CLI

This tutorial offers step-by-step guidance on how to create a subject DID to be used for a holder that will receive a Verifiable Credentials.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../setup-cli.md) for Veramo CLI

## Background

We chose to use [`did:key`](https://github.com/w3c-ccg/did-method-key) for the holder because the [`did:key`](https://w3c-ccg.github.io/did-method-key/) specification has a lot synergy with cheqd did method, [`did:cheqd`](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method).

Using [`did:key`](https://github.com/w3c-ccg/did-method-key) also keeps holder DIDs off-ledger. This is very important for the privacy and data protection of the holder. If holder DIDs and DIDDocs were stored on-ledger, this could constitute a risk where data written within the DIDDoc could not be removed in line with a holder's right to be forgotten.

It is also easily managable by a client agent as a full ledger backup for the writing of a DID / DID Doc is not required, thereby making it easily reproducable in a client environment.

Creating a subject [`did:key`](https://github.com/w3c-ccg/did-method-key) DID can be achieved through both the Veramo CLI, or directly through an application that has been setup to read and write to the cheqd ledger, using the Veramo SDK for cheqd.

## Instructions

### 1. Invoke Veramo CLI DID create function

This off-ledger DID, of type `did:key`, is used for the recipient of the Verifiable Credential and Verifiable Presentation using:

```bash
veramo did create
```

### 2. Select the `did:key` provider and KMS

Select `did:key` as the provider when prompted as well as a key management system (KMS) to use (default is `local`).

The [`agent.yml`](https://github.com/cheqd/did-provider-cheqd/blob/main/agent.yml) file used for the Veramo SDK for cheqd has a preloaded local key management system which is bootstrapped along with the CLI.

The KMS is stored in a local SQLite database.

### 3. Enter an alias for the DID

This is the nickname for your DID, which will be managed by Veramo KMS. Providing a memorable alias will help reference this for future uses, e.g., `Alice off ledger DID`.

Once you have entered this, you will receive a response in a table, e.g:

| provider                  | alias                  | did |   |
| :--------------------- | :----------------------- | :----------------- | :------------------------------ |
| did:key     | Alice off ledger DID  | `did:key:z6MkjxdqDUWoudesywsGYbzGozUeVYiYVArdoqPcMV6m6MG4`                                |

## Next steps

If you got the table above, your off-ledger `did:key` has been created
