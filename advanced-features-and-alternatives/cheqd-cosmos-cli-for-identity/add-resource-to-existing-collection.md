# Publishing a new Resource version

The purpose of this document is to describe how someone can create [a _new_ Resource on under an existing _Collection_](../../guides/did-linked-resources/technical-composition-of-did-linked-resources/creating-a-resource.md).

This tutorial uses the [cheqd Cosmos CLI](https://docs.cheqd.io/node/docs/cheqd-cli), similar to the [creating a new Resource tutorial](create-resource.md).

## Pre-requisites

1. Install the latest stable cheqd-node CLI, either as a [standalone binary](https://github.com/cheqd/cheqd-node/releases/latest) or [Docker container image](https://github.com/cheqd/cheqd-node/pkgs/container/cheqd-node).
2. Acquire test CHEQ tokens through [our testnet faucet](https://testnet-faucet.cheqd.io) (if creating it on our testnet), or [CHEQ tokens](https://app.osmosis.zone/?from=OSMO\&to=CHEQ) (if you plan on creating it on mainnet).
3. An [existing DID + DIDDoc created on cheqd ledger](create-did.md)
4. Having [a Resource already created](create-resource.md) under this DIDDoc Collection

## Adding a new Resource to an existing DIDDoc Collection

### 1. Generate a new UUID for the new Resource version

[UUIDs are used to identify Resources](https://en.wikipedia.org/wiki/Universally_unique_identifier). On Unix systems, the `uuidgen` tool can be used to generate a new UUID:

```bash
$ uuidgen
c8ef6d88-ee0c-4ca4-9fd6-f9e17f6f8f3f
```

### 2. Prepare a file with Resource content

Resource content should be placed in a file and stored locally.

### 3. Publish the new Resource version to the ledger

Resources with the same Collection ID and name are grouped into version sets. Each resource in such a set has a link to the previous version (except the first version) and the next version (if it's not the most recent version).

To create a resource and mark it as a new version within a particular group, we need to use the same `collection-id` and `name` as in the previous version. Links between versions will be created automatically.

New versions have dedicated unique IDs and can be referenced and retrieved as any other resources.

#### Command

```bash
cheqd-noded tx resource create [payload-file] [resource-data-file] [flags]
```

#### Parameters

* `payload-file` - file with JSON formatted payload. The format and structure can be found in example
* `resource-data-file` - file which will be sent to the ledger as a `data`. Can be a picture or an image or whatever you want.
* `flags` - additional parameters like, `--gas` or `--from`. `--fees` should match the price of the [speicfic resource file type defined here](create-resource.md#understanding-resource-fees).

#### Example

```bash
cheqd-noded tx resource create \
--chain-id cheqd \
--keyring-backend test \
--output json \
--fees 10000000000ncheq \
--gas auto \
--gas-adjustment 1.8 \
--from base_account \
[payload.json] [resource.jpeg]
```

where `payload.json` is:

```json
{
  "payload": {
    "data": null,
    "collectionId": "b0ca0b75-ca6a-4674-a261-45f6dd0c9c77",
    "id": "c8ef6d88-ee0c-4ca4-9fd6-f9e17f6f8f3f",
    "name": "TestResource",
    "version": "1.0",
    "resourceType": "TestType",
    "alsoKnownAs": []
  },
  "signInputs": [
    {
      "verificationMethodId": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1",
      "privKey": "y4B5qis9BXUq/mODsrWtS3q5ejOk/okSIXlX1/a9HvuG3PgYmekfQmq3QhJ4JSzN/rkiGCQDNKoTXMmxuXDHbg=="
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

#### Example

```bash
cheqd-noded query resource specific-resource \
    c82f2b02-bdab-4dd7-b833-3e143745d612 \
    c8ef6d88-ee0c-4ca4-9fd6-f9e17f6f8f3f \
    --node https://rpc.cheqd.net:443 --output json
```

#### Result

```json
{
  "resource": {
    "resource": {
      "data": "..."
    },
    "metadata": {
      "collection_id": "c82f2b02-bdab-4dd7-b833-3e143745d612",
      "id": "c8ef6d88-ee0c-4ca4-9fd6-f9e17f6f8f3f",
      "name": "EventBrite Logo",
      "version": "",
      "resource_type": "image/png",
      "also_known_as": [
        {
          "uri": "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612/resources/c8ef6d88-ee0c-4ca4-9fd6-f9e17f6f8f3f",
          "description": "did-url"
        }
      ],
      "media_type": "image/svg+xml",
      "created": "2022-11-17T10:35:23Z",
      "checksum": "a95380f460e63ad939541a57aecbfd795fcd37c6d78ee86c885340e33a91b559",
      "previous_version_id": "3e6bd814-6851-4c8a-b114-c64f035ef590",
      "next_version_id": ""
    }
  }
}
```

Notice that `previous_version_id` is not empty and pointing to the previously created resource with `id` : `3e6bd814-6851-4c8a-b114-c64f035ef590`.

