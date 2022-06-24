# Create Resource

## Tutorial for creating a Resource with cheqd CLI

### Overview

The purpose of this document is to outline how someone can create a Resource on the cheqd network.

This tutorial uses cheqd node CLI to send a DIDDoc.

### Pre-requisites

In order to create a DIDDoc using the instructions outlined in this tutorial, you must be using Ubuntu 20.04 terminal. You'll find all the information required to setup your Ubuntu 20.04 terminal at the end of this tutorial.

If you don't currently have Ubuntu 20.04 installed on your machine, you can use VirtualBox or [Docker](https://github.com/cheqd/cheqd-node/diffs/2?base\_sha=204959755a2a1d4662b1e8d58e2160f17fa4fca8\&branch=DEV-890-cheqd-cli-docs\&commentable=true\&name=DEV-890-cheqd-cli-docs\&pull\_number=283\&qualified\_name=refs%2Fheads%2FDEV-890-cheqd-cli-docs\&sha1=204959755a2a1d4662b1e8d58e2160f17fa4fca8\&sha2=4b36c0a5f767b7d4fb91341bc732d54471702dac\&short\_path=1840e4b\&unchanged=expanded\&w=false#requirements-from-os-side)

Please ensure you are running the correct version that testnet runs. You can check which is the current version of testnet [here](https://rpc.testnet.cheqd.network/abci\_info?).

### How to send a Resource to the testnet

#### 1. Create a DIDDoc

If you already have a DIDDoc and corresponding keys, you can skip this step.

#### 2. Create unique-id for our Resource

UUIDs are used to identify Resources. In unix like systems, `uuidgen` tool can be used to generate a new one:

```
$ uuidgen
e7b662f8-d3f8-4a83-bd00-2cdcd6cc50ab
```

#### 3. Prepare a file with Resource content

Resource content should be placed in a file and stored locally.

#### 4. Send DIDDoc to the pool

Command format:

```
cheqd-noded tx resource create-resource \
    <collection-id> \
    <resource-id> \
    <name> \
    <resource-type> \
    <mime-type> \
    <file-path> \
    <signatyre-1-key-id> \
    <signature-1-data> \
    <other-signatures>
```

Where:

* `<collection-id>` - the last fragment of the corresponding DID
* `<resource-id>` - unique resource ID within the collection. UUID format is used;.
* `<name>` - arbitrary human-readable string used to identify the resource
* `<resource-type>` - valid values are: `CL-Schema`, `JSONSchema2020`
* `<mime-type>` - [mime type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics\_of\_HTTP/MIME\_types) of the resource
* `<file-path>` - path to file with resource content
* `<signatures>` - to create a resource, you need the same set of signatures as for DIDDoc update. Provide signatures the same way as during resource creation or modification.

Example:

```
cheqd-noded tx resource create-resource \
    MjM2NjkKMjM2NjkK \
    49610df5-5998-4b72-b28f-02b7f776156f \
    'CL-Schema 1' \
    CL-Schema\
    application/json \
    schema.json \
    did:cheqd:testnet:MjM2NjkKMjM2NjkK#key1 \
    l6KUjm...jz8Un7QCbrW1KPE6gSyg== \
     --from <your-account> \
     --node https://rpc.cheqd.network:443 \
     --chain-id cheqd-testnet-4 \
     --gas auto \
     --gas-adjustment 1.3 \
     --gas-prices 25ncheq
```

After you execute the command, you will receive `"code": 0"`if the DID was successfully written to the ledger. We can do a full query to check this as well. In case of other error codes, field `raw_logs` can help with figuring out the case. For example:

```
"code":1201,"data":"","raw_log":"failed to execute message; message index: 0: id:cheqd:testnet:fcbarcelona: DID Doc not found"
```

#### 5. Check that Resource was successfully written to the ledger

Finally, to check that the DID was successfully written, we can use the following query:

```
cheqd-noded query resource resource \
    <collection-id> \
    <resource-id> \
    --node https://rpc.cheqd.network:443
```

where:

* `<collection-id>` - the last fragment of the DIDDoc related to the resource
* `<resource-id>` - unique ID of the resource within the collection of resources associated with the DIDDoc

In our example:

```
cheqd-noded query resource resource \
    MjM2NjkKMjM2NjkK \
    49610df5-5998-4b72-b28f-02b7f776156f \
    --node https://rpc.cheqd.network:443
```

**Congratulations! You've created your first, of many, Resources on cheqd!**

### Requirements from OS side

Our target OS system is Ubuntu 20.04.

In this case, for running demo flow, we can use variants: Virtualbox or docker.

For example, let it be a docker image, because it's the fastest way to start playing.

The next command can help:

```
docker run -it --rm -u root --entrypoint bash ghcr.io/cheqd/cheqd-node:0.4.0
```

After that, we need to install needed package for process SSL certificates:

```
apt update && apt install ca-certificates -y
```

Also, it can help to setup your favourite editor, for example `vim` :

```
apt install vim -y
```

The next step is to change user to `cheqd` and restore operator's keys:

```
su cheqd
```

```
cheqd-noded keys add <cheqd-operator-name> --recover
```

where, `cheqd-operator-name` it's name of alias for storing your keys locally, whatever you want.
