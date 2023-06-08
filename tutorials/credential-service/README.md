# 🔁 Credential Service

## Introduction

The Credential Service is a set of APIs for using cheqd's identity functionality in a lightweight and highly efficient way.&#x20;

Therefore, using the Credential Service, the barrier for integrating cheqd DIDs into existing client applications and issuing Credentials based on these DIDs has been greatly reduced. Instead of having to integrate with the cheqd SDK, applications can now create a simple workflow to call the relevant APIs for DID, Credential and DID-Linked Resource operations.

## Definitions

* **Cosmos keys**
  * ECDSA keys used to **pay for on-ledger writes**
* **Identity keys**
  * ED25519 keys used for **signing DID Document** payloads
  * ED25519 keys used for **signing proofs in Verifiable Credentials**
* **Custodian mode**
  * cheqd custodies **both Cosmos AND Identity keys in Veramo KMS**
* **Client-managed mode (coming soon)**
  * cheqd custodies **Cosmos keys in Veramo KMS** but **client controls Identity keys locally**
  * This requires tooling for customers (issuers) to sign transactions in similar flow to DID Registrar

## Custodian mode

We have deployed an instance of a **Veramo Key Management Store (KMS)** which uses a **Postgress DB** (**TypeOrm)** to store **Cosmos AND identity** **keys** in one encrypted table, so  it cannot be read in plaintext.

This design allows us to segment different customers' keys securely and efficiently. The decryption keys are stored as an encrypted secret as part of bootstrapping the backend application. This secret is automatically passed during CRUD operations.

If an issuer is authenticated, they should pass some unique ID, which will authenticate against the pivot table, and fetch their associated keys to begin using the Credential Service.

## Authentication

Soon we will be adding an authentication layer into Credential Service which uses LogTo to facilitate user management.&#x20;

## Get started using Credential Service

* [Swagger file](https://credential-service.cheqd.net/swagger/)
* [GitHub repository](https://github.com/cheqd/credential-service)
* [Walkthrough](walkthrough.md)

