# Overview

This document is describing how to setup `did-resolver` as a proxy client to redirect local queries to another external server. The main start point here is the did-resolver root directory and `docker` inside.

## Run the docker-compose

We are assuming that environment is setup and docker-compose the latest version is installed. For more information how to setup docker you can go to [docker](https://docs.docker.com/engine/install/ubuntu/)

So, for starting up the docker service with redirecting queries to external resource, you can change the default parameters:

```text
# Local address which is used by user application

LOCAL_REDIRECT_FROM=localhost:80

# Address with port of external Cheqd-DID-Resolver.

EXTERNAL_REDIRECT_TO=http://localhost:1313
```

- `LOCAL_REDIRECT_FROM` - it's the local address on your machine which will be used as an input point for queries
- `EXTERNAL_REDIRECT_TO` - it's the external address of the server which you going to use for redirecting to

For example, for using `https://did-resolver.cheqd.dev/` external resolver, the next string can be used for pointing it `EXTERNAL_REDIRECT_TO=https://did-resolver.cheqd.dev/`

After setting up such 2 parameters all you need is just to run:

```bash
$ docker compose --profile light up
```

The example of outputs:

- from docker-compose service:

```text
did-resolver/docker$ docker compose --profile light up
[+] Running 1/0
 ⠿ Container docker-redirect-1  Created                                                                                                                                                                0.0s
Attaching to docker-redirect-1
docker-redirect-1  | Listening to 80, Redirecting HTTP requests to https://did-resolver.cheqd.dev/...
docker-redirect-1  | 172.21.0.1 - - [23/Jun/2022:12:34:32 +0000] "GET /1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY%23key1 HTTP/1.1" 301 170 "-" "curl/7.58.0" "-"
```

- and from client:

```bash
$ curl -L 127.0.0.1/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY%23key1
{"contentStream" : {"controller":"did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY","id":"did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY#key1","publicKeyMultibase":"zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkYsWCo7fztHtepn","type":"Ed25519VerificationKey2020"},"contentMetadata" : {"created":"2022-04-05T11:49:19Z","versionId":"EDEAD35C83E20A72872ACD3C36B7BA42300712FC8E3EEE1340E47E2F1B216B2D"},"dereferencingMetadata" : {"contentType":"application/did+json","retrieved":"2022-06-23T12:34:33Z","did":{"didString":"did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY","methodSpecificId":"zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY","method":"cheqd"}}}
```

- direct request:

```bash
$ curl https://did-resolver.cheqd.dev/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY%23key1 
{"contentStream" : {"controller":"did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY","id":"did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY#key1","publicKeyMultibase":"zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkYsWCo7fztHtepn","type":"Ed25519VerificationKey2020"},"contentMetadata" : {"created":"2022-04-05T11:49:19Z","versionId":"EDEAD35C83E20A72872ACD3C36B7BA42300712FC8E3EEE1340E47E2F1B216B2D"},"dereferencingMetadata" : {"contentType":"application/did+json","retrieved":"2022-06-23T12:36:20Z","did":{"didString":"did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY","methodSpecificId":"zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY","method":"cheqd"}}}
```
