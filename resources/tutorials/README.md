# 📚 Creating a Resource using cheqd Cosmos CLI

The purpose of this document is to outline how someone can create a Resource on the cheqd network using [cheqd Cosmos CLI](../../decentralized-identifiers/cheqd-cosmos-cli/). The process that's followed is similar to what's described in the [high-level Resource creation flow](../creating-a-resource.md).

## Pre-requisites

1. Install the latest stable cheqd-node CLI, either as a [standalone binary](https://github.com/cheqd/cheqd-node/releases/latest) or [Docker container image](https://github.com/cheqd/cheqd-node/pkgs/container/cheqd-cli).
2. Acquire test CHEQ tokens through [our testnet faucet](https://testnet-faucet.cheqd.io) (if creating it on our testnet), or [CHEQ tokens](https://app.osmosis.zone/?from=OSMO\&to=CHEQ) (if you plan on creating it on mainnet).

## Creating a new Resource linked to a DID

### 1. Create a new DID + DIDDoc

_Note: If you already have a DIDDoc and corresponding keys, you can skip this step._

To create a DIDDoc, you can follow the instructions to [create a DID with cheqd Cosmos CLI](../../decentralized-identifiers/cheqd-cosmos-cli/).

Let's assume the DID for the DIDDoc is as follows:

`did:cheqd:mainnet:6h7fjuw37gbf9ioty633bnd7thf65hs1`

### 2. Create a UUID for the Resource

[UUIDs are used to identify Resources](../creating-a-resource.md). On Unix systems, the `uuidgen` tool can be used to generate a new UUID:

```bash
$ uuidgen
e7b662f8-d3f8-4a83-bd00-2cdcd6cc50ab
```

### 3. Prepare a file with Resource content

Resource content should be placed in a file and stored locally.

### 4. Send DIDDoc to the ledger

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

* `<collection-id>`: This should be the **same unique identifier** as that after the namespace of the corresponding DID created in step 1
* `<resource-id>`: Unique resource ID within the collection in UUID format
* `<resource-name>`: Arbitrary human-readable string used to identify the resource
* `<resource-type>`: Resource type, such as `CL-Schema`, `JSONSchema2020`, etc
* `<resource-file>`: Path to file with resource content
* `<ver-method-id-1>`, `<priv-key-1>`, `<ver-method-id-N>`, `<priv-key-N>`: To create a resource, you need the same set of signatures as for carrying out a DIDDoc update. Provide signatures the same way as during resource creation or modification, in Step 1.

#### Example

```bash
cheqd-noded tx resource create-resource \
    --collection-id 6h7fjuw37gbf9ioty633bnd7thf65hs1 \
    --resource-id 49610df5-5998-4b72-b28f-02b7f776156f \
    --resource-name 'universityDegree' \
    --resource-type CL-Schema\
    --resource-file schema.json \
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
    "data":"",
    "raw_log":"failed to execute message; message index: 0: id:cheqd:testnet:fcbarcelona: DID Doc not found"
}
```

### 5. Check that Resource was successfully written to the ledger

Finally, to check that the DID was successfully written, we can use the following query:

```bash
cheqd-noded query resource resource \
    <collection-id> \
    <resource-id> \
    --node https://rpc.cheqd.network:443
```

#### Parameters

* `<collection-id>`: The **same unique identifier** as that after the namespace of the corresponding DID created in step 1
* `<resource-id>`: Unique ID of the resource within the collection of resources associated with the DIDDoc

#### Example

```bash
cheqd-noded query resource resource \
    6h7fjuw37gbf9ioty633bnd7thf65hs1 \
    49610df5-5998-4b72-b28f-02b7f776156f \
    --node https://rpc.cheqd.network:443
```

**Congratulations!** You've successfully created a resource on cheqd ledger; hopefully, the first of many.
