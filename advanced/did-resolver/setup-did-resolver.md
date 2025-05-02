---
description: >-
  Setup the DID Resolver to start resolving or dereferencing cheqd Decentralized
  Identifiers (DIDs) and DID-Linked Resources (DLRs).
---

# Setup DID Resolver

## Resolving `did:cheqd` using our DID Resolver

This page describes how to use and set up the [cheqd DID Resolver](https://github.com/cheqd/did-resolver) software package.

If you do not want to install anything and just want to resolve a `did:cheqd` entry from the ledger, you can load the REST API endpoint for [resolver.cheqd.net](https://resolver.cheqd.net/) in your browser.

Or, make a request from terminal to this hosted REST API:

```bash
curl -X GET https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:55dbc8bf-fba3-4117-855c-1e0dc1d3bb47
```

## Running your own cheqd DID Resolver using Docker

Spinning up a Docker container from the [pre-built `did-resolver` Docker image on Github](https://github.com/cheqd/did-resolver/pkgs/container/did-resolver) is as simple as the command below:

```bash
docker compose -f docker/docker-compose.yml up --detach
```

### Configure resolver settings

To configure the resolver, modify the values under the `environment` section of the [Docker Compose file](https://github.com/cheqd/did-resolver/blob/main/docker/docker-compose.yml). The values that can be edited are as follows:

1. **`MAINNET_ENDPOINT`** : Mainnet Network endpoint as string with the following format" `<networks>,<useTls>,<timeout>`. Example: `grpc.cheqd.net:443,true,5s`
   1. `networks`: A string specifying the Cosmos SDK gRPC endpoint from which the Resolver pulls data. Format: `<resource_url>:<resource_port>`
   2. `useTls`: Specify whether gRPC connection to ledger should use secure or insecure pulls. Default is `true` since gRPC uses HTTP/2 with TLS as the transport mechanism.
   3. `timeout`: Timeout (in seconds) to wait for before any ledger requests are considered to have time out.
2. **`TESTNET_ENDPOINT`** : Testnet Network endpoint as string with the following format" `<networks>,<useTls>,<timeout>`. Example: `grpc.cheqd.network:443,true,5s`
3. **`RESOLVER_LISTENER`**\`: A string with address and port where the resolver listens for requests from clients.
4. **`LOG_LEVEL`**: `debug`/`warn`/`info`/`error` - to define the application log level.

#### gRPC Endpoints used by DID Resolver

Our DID Resolver uses the [Cosmos gRPC endpoint](https://docs.cosmos.network/main/core/grpc_rest) from `cheqd-node` to fetch data. Typically, this would be running on port `9090` on a `cheqd-node` instance.

You can either use [public gRPC endpoints for the cheqd network](https://cosmos.directory/cheqd/nodes) (such as the default ones mentioned above), or point it to your own `cheqd-node` instance by enabling gRPC in the `app.toml` configuration file on a node:

```toml
###############################################################################
###                           gRPC Configuration                            ###
###############################################################################

[grpc]

# Enable defines if the gRPC server should be enabled.
enable = true

# Address defines the gRPC server address to bind to.
address = "0.0.0.0:9090"
```

**Note**: If you're pointing a DID Resolver to your own node instance, by default `cheqd-node` instance gRPC endpoints are _not_ served up with a TLS certificate. This means the `useTls` property would need to be set to `false`, unless you're otherwise using a load balancer that provides TLS connections to the gRPC port.

## Building your own Docker image

### Using Docker Build

You can build your own image using `docker build`

```bash
docker build --file docker/Dockerfile --target resolver . --tag did-resolver:local
```

### Using Docker Compose Build

Uncomment the `build` section in the `docker/docker-compose.yml` file. This relies on the `Dockerfile` above but uses Docker Compose syntax to customise the build:

```yaml
build:
  context: ../
  dockerfile: docker/Dockerfile
  target: resolver
image: did-resolver:local
# image: ghcr.io/cheqd/did-resolver:${IMAGE_VERSION}
```

Make sure you comment out the pre-existing `image` property that pulls in a container image from Github Container Registry, as shown above.

You can also do _just_ a build with:

```bash
docker-compose -f docker/docker-compose.yml --env-file docker/docker-compose.env build --no-cache
```

### Running a custom built image

The instructions to configure and run the resolver are the same as when using the pre-built image.

## Resolving `did:cheqd` via Universal Resolver

The [resolver.cheqd.net](https://resolver.cheqd.net/) API endpoint is run by the cheqd team and only handles `did:cheqd` credentials.

If you want to resolve DIDs from multiple DID methods, the [Universal Resolver](https://github.com/decentralized-identity/universal-resolver) project provides a multi DID method resolver.

### Using a pre-existing Universal Resolver endpoint

You can make resolution requests to a pre-existing Universal Resolver endpoint, such as [dev.uniresolver.io](https://dev.uniresolver.io), to their REST API endpoint:

```bash
curl -X GET https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:55dbc8bf-fba3-4117-855c-1e0dc1d3bb47
```

### Running your own Universal Resolver instance

You can also run your own instance of Universal Resolver, using the Docker Compose file of the project.

The [Universal Resolver quick start guide](https://github.com/decentralized-identity/universal-resolver#quick-start) provides instructions on how to do this:

```bash
git clone https://github.com/decentralized-identity/universal-resolver
cd universal-resolver/
docker-compose -f docker-compose.yml pull
docker-compose -f docker-compose.yml up
```
