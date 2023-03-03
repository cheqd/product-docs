# Create a DID using DID Registrar

## Summary

These instructions will help developers create DIDs on cheqd using the DID Registrar.

## Watch this video for a simple walkthrough

{% embed url="https://www.youtube.com/watch?v=cLrgO5IY8P8" %}

## What you need

1. Any CLI for creating keys and signing payloads, such as [Veramo CLI](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/setup-cli.md);
2. cheqd [DID Registrar Driver and APIs](https://did-registrar.cheqd.net/api-docs/)
3. The [Universal Registrar](https://uniregistrar.io/)
4. 5 minutes!

## Instructions

### 1. Generate public/private keypair

Generate a public/private keypair in a CLI locally. You can use our [Veramo CLI here if you want](https://docs.cheqd.io/identity/guides/software-development-kits-sdks/veramo-sdk-for-cheqd/setup-cli). You can use [these instructions to create your keys](https://docs.cheqd.io/identity/tutorials/did-operations/identity-key-handling).&#x20;

### 2. Generate payload for DIDDoc

Go to ["cheqd helpers" in our Registrar Driver here](https://did-registrar.cheqd.net/api-docs/#/Cheqd%20Helpers/get\_did\_document) and click "try it out" in the top right corner.

### 3. Choose inputs and args

Choose your input parameters for the DIDDoc and paste in the `publicKeyHex` into the template builder - make sure you select "testnet".&#x20;

### 4. Paste into Universal Registrar

Paste the output into the [UniRegistrar frontend](https://uniregistrar.io/) (only the DIDDocument section) and click "create did:cheqd".

### 5. Action for signing payload

This will create an `action` for you to sign the serialized payload again in a CLI. This is a security feature which means you are not passing your private key to the Universal Registrar.

### 6. Sign serialized payload

Sign the serialized payload in your CLI (with Veramo you can use the command `veramo execute -m keyManagerSign)`

### 7. Copy output back into Registrar

Copy the output into the "secret" section of the uniRegistrar frontend and replace the empty field in "signature".

### 8. Add in verificationMethod ID

Copy the verificationMethod ID into the "secret" section too, replacing the example text.

### 9. Click "create - did:cheqd"

This will create your DID if you have done everything correctly!

### 10. Check your DID is live!

You can check your DID on the universal resolver or by going to https://resolver.cheqd.net/1.0/identifiers/<yourDID>