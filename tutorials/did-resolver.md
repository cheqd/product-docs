# 🔄 DID Resolver setup

## Setting up cheqd DID Resolver

This page describes how to use and set up the `full` [cheqd DID Resolver](https://github.com/cheqd/did-resolver) software package.

If you do not want to install anything and just want to resolve a `did:cheqd` entry from the ledger, you can load the REST API endpoint for [resolver.cheqd.net](https://resolver.cheqd.net/) in your browser.

Or, make a request from terminal to this hosted REST API:

```bash
curl -X GET https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY
```

## Setting up via Universal Resolver

[Universal Resolver](https://github.com/decentralized-identity/universal-resolver) is the fastest way to set up a `did:cheqd` method resolver in the context of a multi-method DID Resolver.

The [Universal Resolver quick start guide](https://github.com/decentralized-identity/universal-resolver#quick-start) provides instructions on how to do this:

```bash
git clone https://github.com/decentralized-identity/universal-resolver
cd universal-resolver/
docker-compose -f docker-compose.yml pull
docker-compose -f docker-compose.yml up
```

Please check the [Universal Resolver Driver versions section](https://github.com/decentralized-identity/universal-resolver#drivers) to see which version of the `did:cheqd` method driver is currently available upstream.

## Setting up a standalone cheqd DID Resolver

If you don't want to setup the [full-suite of DID method drivers supported by Universal Resolver](https://github.com/decentralized-identity/universal-resolver#drivers), you can also stand up a standalone instance of cheqd DID Resolver following the instructions below.

This cheqd Resolver instance follows similar REST API endpoints as Universal Resolver specification does.

### Configure resolver settings

To configure the resolver, edit the `config.yaml` file in the root of the `@cheqd/did-resolver` repository. The values that can be edited are as follows:

1. **`ledger`**
   1. `networks`: A string specifying the Cosmos SDK gRPC endpoint from which the Resolver pulls data. Format: `<did-namespace>=<resource_url>:<resource_port>;...`
   2. `useTls`: Specify whether gRPC connection to ledger should use secure or insecure pulls. Default is `true` since gRPC uses HTTP/2 with TLS as the transport mechanism.
   3. `timeout`: Timeout (in seconds) to wait for before any ledger requests are considered to have time out.
2. **`resolver`**
   1. `method`: A string describing DID Method that the resolver uses. Set to `cheqd`.
3. **`api`**
   1. `listener`: A string with address and port where the resolver listens for requests from clients.
   2. `resolverPath`: A string with path for DID Doc resolution. Example: `/1.0/identifiers/:did` for requests like `/1.0/identifiers/did:cheqd:testnet:MTMxDQKMTMxDQKMT`
4. **`logLevel`**: `debug`/`warn`/`info`/`error` - to define the application log level

#### Example `config.yaml` file

```yaml
ledger:
  # Provide gRPC endpoints for one of more networks "namespaces" to fetch DIDs/DIDDocs from
  # Must be in format "namespace=endpoint:port"
  networks: "mainnet=grpc.cheqd.net:443;testnet=grpc.cheqd.network:443"
  # Specify whether gRPC connection to ledger should use secure or insecure pulls
  # default: true
  useTls: true
  # Specify a timeout value
  timeout: "5s"

resolver:
  method: "cheqd"

api:
  listener: "0.0.0.0:1313"
  resolverPath: "/1.0/identifiers/:did"

logLevel: "warn"
```

### Running a DID Resolver container image with Docker Compose

#### Docker Compose command

Spinning up a Docker container from the [pre-built `did-resolver` Docker image on Github](https://github.com/cheqd/did-resolver/pkgs/container/did-resolver) is as simple as the command below:

```bash
docker-compose -f docker/docker-compose.yml --env-file docker/docker-compose.env up --no-build
```

#### Docker Compose environment variable configuration

Environment variables needed in Docker Compose are defined in the `docker/docker-compose.env` file. There are defaults already specified, but you can edit these.

1. `IMAGE_VERSION` (default: `latest`): Version number / tag of the Docker image to run. By default, this is set to pull images from Github Container Registry.
2. `RESOLVER_PORT` (default: `8080`): Port on which the Resolver service is published/exposed on the host machine. Internally mapped to port 8080 in the container.
3. `RESOLVER_HOME_DIR` (default: `/resolver`): Default config directory inside the Docker container

## Custom image builds

### Using `Dockerfile`

Build Docker container image using Dockerfile:

```bash
docker build --file docker/Dockerfile --target resolver . --tag did-resolver:local
```

Run the Docker container (modify according to your own build tags and other desired parameters):

```bash
docker run -it did-resolver:local
```

### Using Docker Compose

Uncomment the `build` section in the `docker/docker-compose.yml` file. This relies on the `Dockerfile` above but uses Docker Compose syntax to customise the build:

```yaml
build:
  context: ../
  dockerfile: docker/Dockerfile
  target: resolver
image: did-resolver:${LOCAL_IMAGE_TAG}
# image: ghcr.io/cheqd/did-resolver:${IMAGE_VERSION}
```

Make sure you comment out the pre-existing `image` property that pulls in a container image from Github Container Registry, as shown above.

Also, take a look at the environment variables in `docker/docker-compose.env` and modify any as needed. Then, when you're ready, you can build and bring the container up in a single step:

```bash
docker-compose -f docker/docker-compose.yml --env-file docker/docker-compose.env up
```

You can also do _just_ a build with:

```bash
docker-compose -f docker/docker-compose.yml --env-file docker/docker-compose.env build --no-cache
```
