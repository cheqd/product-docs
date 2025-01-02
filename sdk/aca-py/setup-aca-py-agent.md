# Setup ACA-Py Agent

## Pre-requisites

Before you begin, ensure you have a DID registrar and resolver running.&#x20;

* For testnet, you can use the cheqd [DID registrar](https://did-registrar.cheqd.net) and [resolver](https://resolver.cheqd.net/).&#x20;
* For mainnet, you can run your own cheqd DID registrar with the correct mnemonic configuration. For details check [here](https://github.com/cheqd/did-registrar).

## Configuration

* The DID Registrar and the DID Resolver URL can be passed via a `plugin-config.yml`.
* The plugin works only with `askar-anoncreds` wallet type.
* Using a Postgres DB as wallet storage type is also recommended.

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

