# Setup ACA-Py Agent

## Pre-requisites

Before you begin, ensure you have a DID registrar and resolver running.&#x20;

* For testnet, you can use the [Universal Registrar](https://github.com/decentralized-identity/universal-registrar/) (with cheqd [DID registrar](https://did-registrar.cheqd.net) driver) and a [resolver](https://resolver.cheqd.net/).&#x20;
* For mainnet, you must run your own cheqd DID registrar driver with the correct mnemonic configuration. For details check [here](https://github.com/cheqd/did-registrar).

## Configuration

* The Universal Registrar and the DID Resolver URLs can be passed via a `plugin-config.yml`.
* The plugin works only with `askar-anoncreds` wallet type.
* Using a Postgres DB as wallet storage type is also recommended.
* The Cheqd DID Registrar driver must be configured and run as the `driver-did-cheqd` service in your deployment. The Cheqd DID Registrar driver must be run as `driver-did-cheqd`&#x20;

## **Universal Registrar Integration**

With the latest update, the plugin now supports **dynamic DID registration** using the **Universal Registrar**. This removes the need for separate configurations for different DID methods, all you have to do is run the appropriate driver.

**Key Benefits:**\
✔️ **Multi-method DID support** – Enables DID registration for different DID methods via a single interface.\
✔️ **Simplified setup** – Reduces configuration overhead by dynamically handling DID registrations.\
✔️ **Interoperability** – Works seamlessly with the supported DID methods via drivers.

For further configuration and usage examples, refer to the [Universal Registrar documentation](https://docs.godiddy.com/en/apis/universal-registrar).

## Build the Agent and deploy

Build the ACA-Py Agent docker image with the plugin, and then deploy on your choice of infrastructure.&#x20;

Example Dockerfile:

```docker
FROM ghcr.io/openwallet-foundation/acapy:py3.12-1.1.0

USER root

# install plugins as binaries
RUN pip install git+https://github.com/openwallet-foundation/acapy-plugins@main#subdirectory=cheqd

USER $user
COPY ./configs configs

ENTRYPOINT ["aca-py"]
```

Sample configs are available [here](https://github.com/openwallet-foundation/acapy-plugins/tree/main/cheqd/docker).

## Next steps

Now that your ACA-Py agent is successfully set up to work with cheqd, try following our tutorials for creating a new DID or issuing Verifiable Credentials.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create a DID</strong></mark></td><td>Create an Issuer DID using the did:cheqd DID method.</td><td><a href="decentralized-identifiers-dids/create-a-did.md">create-a-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue a Verifiable Credential</strong></mark></td><td>Issue a Verifiable Credential using ACA-Py signed by a cheqd DID.</td><td><a href="verifiable-credentials-and-presentations/issue-a-verifiable-credential.md">issue-a-verifiable-credential.md</a></td></tr></tbody></table>

