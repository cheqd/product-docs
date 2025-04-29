---
description: Set up your ACA-Py agent to start using cheqd.
---

# Setup ACA-Py Agent

## Pre-requisites

Before you begin, ensure you have a DID registrar and resolver running.&#x20;

* For testnet, you can use the [Universal Registrar](https://github.com/decentralized-identity/universal-registrar/) (with cheqd [DID registrar](https://did-registrar.cheqd.net) driver) and a [resolver](https://resolver.cheqd.net/).&#x20;
* For mainnet, you must run your own cheqd DID registrar driver with the correct mnemonic configuration. Details are provided below.

## Configuration

{% hint style="info" %}
All sample configs and docker-compose files are available [here](https://github.com/openwallet-foundation/acapy-plugins/tree/main/cheqd/docker).
{% endhint %}

### Cheqd DID Registrar Driver

To enable DID registration on Cheqd, you must deploy the Cheqd DID Registrar driver as a dedicated service. The fastest way to run the Cheqd Registrar driver is using the published docker image, for other options or to build your own container, check the [GitHub repository](https://github.com/cheqd/did-registrar).

#### Steps to deploy the Cheqd Registrar Driver

1.  Add the following under `services`  section of your docker-compose file.

    ```yaml
    driver-did-cheqd:
        image: ghcr.io/cheqd/did-registrar:production-latest
        platform: linux/amd64
        ports:
          - "9089:3000"
        restart: on-failure
        environment:
          - FEE_PAYER_TESTNET_MNEMONIC=${FEE_PAYER_TESTNET_MNEMONIC}
          - FEE_PAYER_MAINNET_MNEMONIC=${FEE_PAYER_MAINNET_MNEMONIC}
    ```
2.  Set the environment variables.

    ```bash
    export FEE_PAYER_TESTNET_MNEMONIC="your testnet mnemonic phrase ..."
    export FEE_PAYER_MAINNET_MNEMONIC="your mainnet mnemonic phrase ..."
    ```
3.  Run the driver.

    ```bash
    docker-compose up -d
    ```

{% hint style="warning" %}
The Cheqd Registrar must be run as `driver-did-cheqd` because the Universal Registrar is configured with that name by default.
{% endhint %}

### Universal Registrar Setup

The **Universal Registrar** allows dynamic DID registration for multiple DID methods, including Cheqd. Setting it up ensures seamless DID creation without manual configurations for different DID methods. The fastest way to run the Universal Registrar is using the published docker container, for other options check the [Universal Registrar GitHub repository](https://github.com/decentralized-identity/universal-registrar).

#### Steps to setup Universal Registrar for Cheqd

1.  Add the following under `services`  section of your docker-compose file.

    ```yaml
    did_registrar:
        image: universalregistrar/uni-registrar-web:latest
        platform: linux/amd64
        ports:
          - "9080:9080"
        depends_on:
          - driver-did-cheqd
    ```
2.  Start the Universal Registrar Service.

    ```bash
    docker-compose up -d
    ```
3.  Confirm that the Cheqd Driver properties are correct and matches your settings.

    <pre class="language-bash"><code class="lang-bash"><strong>curl -X GET http://localhost:9080/1.0/properties
    </strong></code></pre>

### Cheqd DID Resolver

To run your own DID Resolver, the following settings must be added to your docker-compose file

#### Steps to deploy the Cheqd Resolver

1.  Add the following under `services`  section of your docker-compose file.

    ```yaml
    did_resolver:
        image: ghcr.io/cheqd/did-resolver:latest
        platform: linux/amd64
        ports:
          - target: 8080
            published: 8080
            mode: host
        restart: on-failure
        environment:
          MAINNET_ENDPOINT: "grpc.cheqd.net:443,true,5s"
          TESTNET_ENDPOINT: "grpc.cheqd.network:443,true,5s"
          LOG_LEVEL: "warn"
          RESOLVER_LISTENER: "0.0.0.0:8080"
    ```
2.  Run the resolver.

    ```bash
    docker-compose up -d
    ```

### Plugin Configuration

Cheqd ACA-Py Plugin supports configuring the **Universal Registrar** and **DID Resolver** **URLs** via a `plugin-config.yml` file. These settings define how the agent interacts with the Cheqd network for DID operations.

#### **Example `plugin-config.yml`:**

```yaml
resolver_url: "http://localhost:8080/1.0/identifiers/"
registrar_url: "http://localhost:9080/1.0/"
```

Update the URLs if you have your hosted versions or using the Universal Resolver.

### Wallet Compatibility

The Cheqd plugin **only works with the `askar-anoncreds` wallet type**. Askar-anoncreds is the preferred wallet due to its support for **AnonCreds** and enhanced security features.

#### **Setting Up Askar Wallet in ACA-Py**

When starting ACA-Py, ensure that the `wallet-type` is set to `askar`. Example:

```bash
aca-py start --wallet-type askar-anoncreds --wallet-storage-type postgres_storage
```

### Recommended Wallet Storage: PostgreSQL

Using **PostgreSQL as a wallet storage backend** is recommended for scalability and data persistence.

#### **Setting Up PostgreSQL for ACA-Py**

1. Install PostgreSQL and create a database for ACA-Py.
2.  Configure ACA-Py to use PostgreSQL, add the following to `./configs/settings.yml`:

    ```yaml
    wallet-name: issuer
    wallet-key: somesecret
    wallet-storage-type: postgres_storage
    wallet-storage-creds: '{"account":"postgres","password":"postgres","admin_account":"postgres","admin_password":"postgres"}'
    wallet-storage-config: '{"url":"localhost:5432","max_connections":5}'
    ```
3.  Start ACA-Py with PostgreSQL-backed wallet storage.

    ```bash
    aca-py start --arg-file ./configs/settings.yml
    ```

## Build the Agent and deploy

1.  Example Dockerfile:

    ```docker
    FROM ghcr.io/openwallet-foundation/acapy:py3.12-1.1.0

    USER root

    # install plugins as binaries
    RUN pip install git+https://github.com/openwallet-foundation/acapy-plugins@main#subdirectory=cheqd

    USER $user
    COPY ./configs configs

    ENTRYPOINT ["aca-py"]
    ```
2.  Build the ACA-Py Agent docker image with the plugin.

    ```bash
    docker build -t issuer-agent .
    ```
3.  Deploy the agent. Sample docker-compose is below.

    ```yaml
    issuer:
        image: issuer-agent:latest
        ports:
          - "3001:3001"
          - "3002:3002"
        command: >
          start --arg-file ./configs/settings.yml
        healthcheck:
          test: curl -s -o /dev/null -w '%{http_code}' "http://localhost:3001/status/live" | grep "200" > /dev/null
          start_period: 30s
          interval: 7s
          timeout: 5s
          retries: 5
        depends_on:
          - did_registrar
          - did_resolver
    ```
4. Where all the plugin settings are populated in `./configs/settings.yml` , a sample file is [here](https://github.com/openwallet-foundation/acapy-plugins/blob/main/cheqd/docker/default.yml).
5.  Run the Agent.

    ```bash
    docker-compose up -d
    ```

## Next steps

Now that your ACA-Py agent is successfully set up to work with cheqd, try following our tutorials for creating a new DID or issuing Verifiable Credentials.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create a DID</strong></mark></td><td>Create an Issuer DID using the did:cheqd DID method.</td><td><a href="dids/create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue a Verifiable Credential</strong></mark></td><td>Issue a Verifiable Credential using ACA-Py signed by a cheqd DID.</td><td><a href="credentials/issue-credential.md">issue-credential.md</a></td></tr></tbody></table>

