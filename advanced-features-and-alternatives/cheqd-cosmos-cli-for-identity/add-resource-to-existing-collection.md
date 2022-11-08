# 📚 Publishing a new Resource version

The purpose of this document is to describe how someone can create [a _new_ Resource on under an existing _Collection_](../../guides/resources/creating-a-resource.md).

This tutorial uses the [cheqd Cosmos CLI](https://docs.cheqd.io/node/docs/cheqd-cli), similar to the [creating a new Resource tutorial](tutorials.md).

## Pre-requisites

1. Install the latest stable cheqd-node CLI, either as a [standalone binary](https://github.com/cheqd/cheqd-node/releases/latest) or [Docker container image](https://github.com/cheqd/cheqd-node/pkgs/container/cheqd-node).
2. Acquire test CHEQ tokens through [our testnet faucet](https://testnet-faucet.cheqd.io) (if creating it on our testnet), or [CHEQ tokens](https://app.osmosis.zone/?from=OSMO&to=CHEQ) (if you plan on creating it on mainnet).
3. An [existing DID + DIDDoc created on cheqd ledger](cheqd-cosmos-cli.md)
4. Having [a Resource already created](tutorials.md) under this DIDDoc Collection

## Adding a new Resource to an existing DIDDoc Collection

### 1. Generate a new UUID for the new Resource version

[UUIDs are used to identify Resources](../../guides/resources/creating-a-resource.md). On Unix systems, the `uuidgen` tool can be used to generate a new UUID:

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
cheqd-noded tx resource create-resource \
    --collection-id <collection-id>
    --resource-id <resource-id>
    --resource-name <resource-name>
    --resource-type <resource-type>
    --resource-file <resource-file>
    <ver-method-id-1> <priv-key-1>
    <ver-method-id-N> <priv-key-N>
```

#### Parameters

- `<collection-id>`: This should be the **same unique identifier** as that after the namespace of the corresponding DID created in step 1
- `<resource-id>`: Unique resource ID within the collection in UUID format
- `<resource-name>`: Arbitrary human-readable string used to identify the resource
- `<resource-type>`: Resource type, such as `CL-Schema`, `JSONSchema2020`, etc
- `<resource-file>`: Path to file with resource content
- `<ver-method-id-1>`, `<priv-key-1>`, `<ver-method-id-N>`, `<priv-key-N>`: To create a resource, you need the same set of signatures as for carrying out a DIDDoc update. Provide signatures the same way as during resource creation or modification, in Step 1.

#### Example

```bash
cheqd-noded tx resource create-resource \
    --collection-id 6h7fjuw37gbf9ioty633bnd7thf65hs1 \
    --resource-id c8ef6d88-ee0c-4ca4-9fd6-f9e17f6f8f3f \
    --resource-name 'universityDegree' \
    --resource-type CL-Schema\
    --resource-file schema-v2.json \
    did:cheqd:mainnet:6h7fjuw37gbf9ioty633bnd7thf65hs1#key1 \
    l6KUjm...jz8Un7QCbrW1KPE6gSyg== \
     --from <your-account> \
     --node https://rpc.cheqd.network:443 \
     --chain-id cheqd-mainnet-1 \
     --gas auto \
     --gas-adjustment 1.3 \
     --gas-prices 25ncheq
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
cheqd-noded query resource resource \
    <collection-id> \
    <resource-id> \
    --node https://rpc.cheqd.network:443
```

#### Parameters

- `<collection-id>`: The **same unique identifier** as that after the namespace of the corresponding DID created in step 1
- `<resource-id>`: Unique ID of the resource within the collection of resources associated with the DIDDoc

#### Example

```bash
cheqd-noded query resource resource \
    6h7fjuw37gbf9ioty633bnd7thf65hs1 \
    c8ef6d88-ee0c-4ca4-9fd6-f9e17f6f8f3f \
    --node https://rpc.cheqd.network:443
```

#### Result

```json
"resource": {
    "header": {
        "collection_id": "6h7fjuw37gbf9ioty633bnd7thf65hs1",
        "id": "c8ef6d88-ee0c-4ca4-9fd6-f9e17f6f8f3f",
        "name": "universityDegree",
        "resource_type": "CL-Schema",
        "media_type": "application/json",
        "created": "2022-08-04T17:18:20Z",
        "checksum": "eyAiY29udGVudCI6ICJ0ZXN0IGRhdGEiIH0K47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=",
        "previous_version_id": "49610df5-5998-4b72-b28f-02b7f776156f",
        "next_version_id": ""
    },
    "data": "..."
}
```

Notice that `header.previous_version_id` was set to the ID of the previous version. Correspondingly, `next_version` was updated to `c8ef6d88-ee0c-4ca4-9fd6-f9e17f6f8f3f` for the previous version of the resource.
