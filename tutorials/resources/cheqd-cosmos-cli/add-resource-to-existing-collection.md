# Add new Resource to existing Collection

## Tutorial for creating a new Resource version within an existing Collection using cheqd CLI

### Overview

The purpose of this document is to outline how someone can create a new Resource version, within an existing Collection on the cheqd network.

This tutorial uses cheqd node CLI.

### Pre-requisites

In order to create a resource version using the instructions outlined in this tutorial, you must be using Ubuntu 20.04 terminal. You'll find all the information required to setup your Ubuntu 20.04 terminal at the end of this tutorial.

If you don't currently have Ubuntu 20.04 installed on your machine, you can use VirtualBox or [Docker](https://github.com/cheqd/cheqd-node/diffs/2?base\_sha=204959755a2a1d4662b1e8d58e2160f17fa4fca8\&branch=DEV-890-cheqd-cli-docs\&commentable=true\&name=DEV-890-cheqd-cli-docs\&pull\_number=283\&qualified\_name=refs%2Fheads%2FDEV-890-cheqd-cli-docs\&sha1=204959755a2a1d4662b1e8d58e2160f17fa4fca8\&sha2=4b36c0a5f767b7d4fb91341bc732d54471702dac\&short\_path=1840e4b\&unchanged=expanded\&w=false#requirements-from-os-side)

Please ensure you are running the correct version that mainnet runs. You can [check which is the current version of mainnet here](https://rpc.mainnet.cheqd.network/abci\_info?).

### How to create a new Resource version within an existing collection on cheqd mainnet

#### 1. Create a DIDDoc

If you already have a DIDDoc and corresponding keys, you can skip this step.

To create a DIDDoc, you can follow the [create DIDDoc tutorial here](../../dids/cheqd-cosmos-cli/create-did-and-did-document.md).&#x20;

Let's presume the DID for the DID Doc is as follows:

`did:cheqd:mainnet:6h7fjuw37gbf9ioty633bnd7thf65hs1`

#### 2. Create a Resource

If you already have a Resource, you can skip this step.

To create a Resource, you can follow the [create Resource tutorial here](create-resource.md).&#x20;

Let's presume ID and the name for the Resource are as follows:

* Resource ID: `49610df5-5998-4b72-b28f-02b7f776156f`
* Resource Name: `universityDegree`

#### 3. Generate a unique-id for the new Resource version

UUIDs are used to identify Resources. In unix like systems, `uuidgen` tool can be used to generate a new one:

```bash
$ uuidgen
c8ef6d88-ee0c-4ca4-9fd6-f9e17f6f8f3f
```

#### 4. Prepare a file with Resource content

Resource content should be placed in a file and stored locally.

#### 5. Send the new version to the pool

Resources with the same Collection ID and name are grouped into version sets. Each resource in such a set has a link to the previous version (except the first version) and the next version (if it's not the most recent version).

To create a resource and mark it as a new version within a particular group, we need to use the same `collection-id` and `name` as in the previous version. Links between versions will be created automatically.

New versions have dedicated unique IDs and can be referenced and retrieved as any other resources.

Command format:

```bash
cheqd-noded tx resource create-resource \
    --collection-id <collection-id>     <- Must be the same as for the previous version of the resource
    --resource-id <resource-id>
    --resource-name <resource-name>     <- Must be the same as for the previous version of the resource
    --resource-type <resource-type>
    --resource-file <resource-file>
    <ver-method-id-1> <priv-key-1>
    <ver-method-id-N> <priv-key-N>
```

Where:

* `<collection-id>` - this should be the **same unique identifier** as that after the namespace of the corresponding DID created in step 1
* `<resource-id>` - unique resource ID within the collection. UUID format is used
* `<resource-name>` - arbitrary human-readable string used to identify the resource
* `<resource-type>` - suggested values are: `CL-Schema`, `JSONSchema2020`
* `<resource-file>` - path to file with resource content
* `<ver-method-id-1>`, `<priv-key-1>`, `<ver-method-id-N>`,  `<priv-key-N>` - to create a resource, you need the same set of signatures as for carrying out a DIDDoc update. Provide signatures the same way as during resource creation or modification, in step 1.

Example:

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

After you execute the command, you will receive `"code": "0"` if a new version of the Resource was successfully written to the ledger. We can do a full query to check this as well. In case of other error codes, field `raw_logs` can help with figuring out the case. For example:

```json
{"code":1201,"data":"","raw_log":"failed to execute message; message index: 0: id:cheqd:testnet:fcbarcelona: DID Doc not found"}
```

#### 5. Check that Resource was successfully written to the ledger

Finally, to check that the DID was successfully written, we can use the following query:

```bash
cheqd-noded query resource resource \
    <collection-id> \
    <resource-id> \
    --node https://rpc.cheqd.network:443
```

where:

* `<collection-id>` - the **same unique identifier** as that after the namespace of the corresponding DID created in step 1
* `<resource-id>` - unique ID of the resource within the collection of resources associated with the DIDDoc

In our example:

```bash
cheqd-noded query resource resource \
    6h7fjuw37gbf9ioty633bnd7thf65hs1 \
    c8ef6d88-ee0c-4ca4-9fd6-f9e17f6f8f3f \
    --node https://rpc.cheqd.network:443
```


Pay attention to the result:


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

**Congratulations! You've created a new version of the existing Resource on cheqd!**

### Requirements from OS side

Our target OS system is Ubuntu 20.04.

In this case, for running demo flow, we can use variants: Virtualbox or docker.

For example, let it be a docker image, because it's the fastest way to start playing.

The next command can help:

```bash
docker run -it --rm -u root --entrypoint bash ghcr.io/cheqd/cheqd-node:0.4.0
```

After that, we need to install needed package for process SSL certificates:

```bash
apt update && apt install ca-certificates -y
```

Also, it can help to setup your favourite editor, for example `vim` :

```bash
apt install vim -y
```

The next step is to change user to `cheqd` and restore operator's keys:

```bash
su cheqd
```

```bash
cheqd-noded keys add <cheqd-operator-name> --recover
```

where, `cheqd-operator-name` it's name of alias for storing your keys locally, whatever you want.
