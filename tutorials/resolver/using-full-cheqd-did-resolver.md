# Using full cheqd DID resolver

## Overview

This page describes how to use and set up the `full` [cheqd DID Resolver application](https://github.com/cheqd/did-resolver).

## Setup

Clone the repository

```bash
git clone git@github.com:cheqd/did-resolver.git
cd did-resolver/docker
```

Build a docker container

```bash
docker compose --profile full build
```

Start the docker container

```bash
docker compose --profile full up
```

## Configure application

To configure use `config.yaml` file in the root of the project:

```yaml
ledger:
  networks: "mainnet=grpc.cheqd.net:443;testnet=grpc.cheqd.network:443"
  useTls: true
  timeout: "5s"

resolver:
  method: "cheqd"

api:
  listener: "0.0.0.0:1313"
  resolverPath: "/1.0/identifiers/:did"

logLevel: "warn"
```

### Settings

* **ledger**
  * **networks** : A string describing the sources that the resolver will use and their routing. Format `<did-namespace>=<resource_url>:<resource_port>;...`
    * Example: `mainnet=grpc.cheqd.net:443;testnet=grpc.cheqd.network:443`
  * **useTls**
  * **timeout** : Time of waiting answer from a ledger (URLs from `network`)
* **resolver**
  * **method** : A string describing DID Method that the resolver uses.
* **api**
  * **listener** : A string with address and port where the resolver listens for requests from clients.
    * Example: `0.0.0.0:1313`
    * *Warning*: If you use docker compose for setting up the resolver, please use `RESOLVER_PORT` environment variable.
  * **resolverPath** : A string with path for DID Doc resolution.
    * Example: `/1.0/identifiers/:did` for requests like `/1.0/identifiers/did:cheqd:testnet:MTMxDQKMTMxDQKMT`
* **logLevel** : `debug`/`warn`/`info`/`error` - to define the application log level

## Configure docker compose

The environment variables can be used for configuring setup with a docker compose.
You can change them in `./docker/.env` file
or setting up inline with docker compose `full` profile.

Example. Setup with setting environment variables inline.

```bash
RESOLVER_PORT="1313" docker compose --profile full up
```

### Docker compose settings

* **RESOLVER_PORT** (by default: `1313`) - port which will be used for incoming requests on resolver side.
* **CHEQD_RESOLVER_HOME_DIR** (by default: `/home/cheqd-resolver`) - a directory for config file **inside** a docker container.
