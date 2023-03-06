# 📚 Creating a Resource using cheqd Cosmos CLI

The purpose of this document is to outline how someone can create a Resource on the cheqd network using [cheqd Cosmos CLI](create-did.md). The process that's followed is similar to what's described in the [high-level Resource creation flow](../../guides/did-linked-resources/technical-composition-of-did-linked-resources/creating-a-resource.md).

## Pre-requisites

1. Install the latest stable cheqd-node CLI, either as a [standalone binary](https://github.com/cheqd/cheqd-node/releases/latest) or [Docker container image](https://github.com/cheqd/cheqd-node/pkgs/container/cheqd-node).
2. Acquire test CHEQ tokens through [our testnet faucet](https://testnet-faucet.cheqd.io) (if creating it on our testnet), or [CHEQ tokens](https://app.osmosis.zone/?from=OSMO\&to=CHEQ) (if you plan on creating it on mainnet).

## Creating a new Resource linked to a DID

### 1. Create a new DID + DIDDoc

_Note: If you already have a DIDDoc and corresponding keys, you can skip this step._

To create a DIDDoc, you can follow the instructions to [create a DID with cheqd Cosmos CLI](create-did.md).

Let's assume the DID for the DIDDoc is as follows:

`did:cheqd:mainnet:6h7fjuw37gbf9ioty633bnd7thf65hs1`

### 2. Create a UUID for the Resource

[UUIDs are used to identify Resources](https://en.wikipedia.org/wiki/Universally_unique_identifier). On Unix systems, the `uuidgen` tool can be used to generate a new UUID:

```bash
$ uuidgen
e7b662f8-d3f8-4a83-bd00-2cdcd6cc50ab
```

### 3. Prepare a file with Resource content

Resource content should be placed in a file and stored locally.

#### Command

```bash
cheqd-noded tx resource create [payload-file] [resource-data-file] [flags]
```

#### Parameters

* `payload-file` - file with JSON formatted payload. The format and structure can be found in example
* `resource-data-file` - file which will be sent to the ledger as a `data`. Can be a picture or an image or whatever you want.
* `flags` - additional parameters like, `--gas` or `--from`.

Example input:

```bash
cheqd-noded tx resource create \
  --chain-id cheqd \
  --keyring-backend test \
  --output json \
  --fees 10000000000ncheq \
  --gas auto \
  --gas-adjustment 1.8 \
  --from base_account \
  "payloadfile.json" data.jpeg
```

where `payloadfile.json` is:

```json
{
  "Payload": {
    "data": null,
    "collectionId": "b0ca0b75-ca6a-4674-a261-45f6dd0c9c77",
    "id": "5e16a3f9-7c6e-4b6b-8e28-20f56780ee25",
    "name": "TestResource",
    "version": "1.0",
    "resourceType": "TestType",
    "alsoKnownAs": []
  },
  "SignInputs": [
    {
      "VerificationMethodID": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1",
      "PrivKey": "y4B5qis9BXUq/mODsrWtS3q5ejOk/okSIXlX1/a9HvuG3PgYmekfQmq3QhJ4JSzN/rkiGCQDNKoTXMmxuXDHbg=="
    }
  ]
}
```

After you execute the command, you will receive `"code": 0"` if the resource was successfully written to the ledger.

Otherwise, the `raw_logs` field in the response can help figure out why something went wrong. For example:

```json
{
  "code": 1201,
  "data": "",
  "raw_log": "failed to execute message; message index: 0: id:cheqd:testnet:fcbarcelona: DID Doc not found"
}
```

### 4. Check that new Resource version was successfully written to the ledger

Finally, to check that the resource was successfully written, we can use the following query:

```bash
cheqd-noded query resource specific-resource \
    <collection-id> \
    <resource-id> \
    --node https://rpc.cheqd.network:443
```

#### Parameters

* `<collection-id>`: The **same unique identifier** as that after the namespace of the corresponding DID created in step 1
* `<resource-id>`: Unique ID of the resource within the collection of resources associated with the DIDDoc

Example input:

```bash
cheqd-noded query resource specific-resource \
    c82f2b02-bdab-4dd7-b833-3e143745d612 \
    3e6bd814-6851-4c8a-b114-c64f035ef590 \
    --node https://rpc.cheqd.net:443 --output json
```

Ouput:

```json
{
  "resource": {
    "resource": {
      "data": "..."
    },
    "metadata": {
      "collectionId": "c82f2b02-bdab-4dd7-b833-3e143745d612",
      "id": "3e6bd814-6851-4c8a-b114-c64f035ef590",
      "name": "EventBrite Logo",
      "version": "",
      "resourceType": "image/png",
      "alsoKnownAs": [
        {
          "uri": "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612/resources/3e6bd814-6851-4c8a-b114-c64f035ef590",
          "description": "did-url"
        }
      ],
      "mediaType": "image/svg+xml",
      "created": "2022-11-17T10:35:23Z",
      "checksum": "a95380f460e63ad939541a57aecbfd795fcd37c6d78ee86c885340e33a91b559",
      "previousVersionId": "",
      "nextVersionId": ""
    }
  }
}
```

Notice that both `previous_version_id` and `next_version_id` are empty now cause there is only one resource in such collection `c82f2b02-bdab-4dd7-b833-3e143745d612`.

### 5. Additional queries to the ledger

There are also 2 annother commands for getting resource from ledger depending what the actual info is needed.

#### Get metadata

Here is the command which allows to get only metadata information without getting the whole resource.
The format of call is:

```bash
cheqd-noded query resource metadata [collection-id] [resource-id] [flags]
```

Parameters `collection-id` and `resource-id` have the same meaning as before.

Example input:

```bash
cheqd-noded query resource metadata \
    c82f2b02-bdab-4dd7-b833-3e143745d612 \
    3e6bd814-6851-4c8a-b114-c64f035ef590 \
    --node https://rpc.cheqd.net:443 --output json
```

Output:

```json
{
  "resource": {
    "collection_id": "c82f2b02-bdab-4dd7-b833-3e143745d612",
    "id": "3e6bd814-6851-4c8a-b114-c64f035ef590",
    "name": "EventBrite Logo",
    "version": "",
    "resource_type": "image/png",
    "also_known_as": [
      {
        "uri": "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612/resources/3e6bd814-6851-4c8a-b114-c64f035ef590",
        "description": "did-url"
      }
    ],
    "media_type": "image/svg+xml",
    "created": "2022-11-17T10:35:23Z",
    "checksum": "a95380f460e63ad939541a57aecbfd795fcd37c6d78ee86c885340e33a91b559",
    "previous_version_id": "",
    "next_version_id": ""
  }
}
```

#### Get collection of resources

For querying all the resources but only metadata there is a special command:

```bash
cheqd-noded query resource collection-metadata [collection-id] [flags]
```

As the main parameter it requires only `collection-id`.

Example input:

```bash
cheqd-noded query resource collection-metadata c82f2b02-bdab-4dd7-b833-3e143745d612  --node https://rpc.cheqd.net:443 --output json
```

Ouput:

```json
{
  "resources": [
    {
      "collection_id": "c82f2b02-bdab-4dd7-b833-3e143745d612",
      "id": "3e6bd814-6851-4c8a-b114-c64f035ef590",
      "name": "EventBrite Logo",
      "version": "",
      "resource_type": "image/png",
      "also_known_as": [
        {
          "uri": "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612/resources/3e6bd814-6851-4c8a-b114-c64f035ef590",
          "description": "did-url"
        }
      ],
      "media_type": "image/svg+xml",
      "created": "2022-11-17T10:35:23Z",
      "checksum": "a95380f460e63ad939541a57aecbfd795fcd37c6d78ee86c885340e33a91b559",
      "previous_version_id": "",
      "next_version_id": ""
    },
    {
      "collection_id": "c82f2b02-bdab-4dd7-b833-3e143745d612",
      "id": "9447c669-0ba1-4989-bd10-a85cc298aace",
      "name": "Discord Logo",
      "version": "",
      "resource_type": "image/png",
      "also_known_as": [
        {
          "uri": "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612/resources/9447c669-0ba1-4989-bd10-a85cc298aace",
          "description": "did-url"
        }
      ],
      "media_type": "image/png",
      "created": "2022-11-17T10:35:51Z",
      "checksum": "b2939df5a48f422fc9d62f270c182f07b5fd5a7a334478ea73af4fdb5eb12d3b",
      "previous_version_id": "",
      "next_version_id": ""
    },
    {
      "collection_id": "c82f2b02-bdab-4dd7-b833-3e143745d612",
      "id": "ba4d1a8b-6395-4b96-8492-aaf2800d5727",
      "name": "Twitter Logo",
      "version": "",
      "resource_type": "image/png",
      "also_known_as": [
        {
          "uri": "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612/resources/ba4d1a8b-6395-4b96-8492-aaf2800d5727",
          "description": "did-url"
        }
      ],
      "media_type": "image/png",
      "created": "2022-11-17T10:36:26Z",
      "checksum": "aeb8f203a6a21cca668c5c8983dfe86b3cf95add102305da8208100595d69800",
      "previous_version_id": "",
      "next_version_id": ""
    },
    {
      "collection_id": "c82f2b02-bdab-4dd7-b833-3e143745d612",
      "id": "cb3f5f64-c138-4309-b9ea-8d658b0ae28e",
      "name": "IIW Event Logo",
      "version": "",
      "resource_type": "image/png",
      "also_known_as": [
        {
          "uri": "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612/resources/cb3f5f64-c138-4309-b9ea-8d658b0ae28e",
          "description": "did-url"
        }
      ],
      "media_type": "image/png",
      "created": "2022-11-17T10:36:37Z",
      "checksum": "d48e158b915eae31ba2db640bd4aac7f82179ee5ca0263a8fe99012d4b02cf48",
      "previous_version_id": "",
      "next_version_id": ""
    },
    {
      "collection_id": "c82f2b02-bdab-4dd7-b833-3e143745d612",
      "id": "e2651dd2-7ca7-44f1-9ba5-57a77747d9b4",
      "name": "GitHub Logo",
      "version": "",
      "resource_type": "image/png",
      "also_known_as": [
        {
          "uri": "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612/resources/e2651dd2-7ca7-44f1-9ba5-57a77747d9b4",
          "description": "did-url"
        }
      ],
      "media_type": "image/png",
      "created": "2022-11-17T10:36:09Z",
      "checksum": "22ed95ff774cee8427c86b60288af4077b3b26424c758bec95a34aa8b7a88937",
      "previous_version_id": "",
      "next_version_id": ""
    }
  ],
  "pagination": null
}
```

**Congratulations!** You've successfully created a resource on cheqd ledger; hopefully, the first of many.
