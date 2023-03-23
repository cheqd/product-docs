# Create a new Resource version within existing Collection

Follow these instructions to create a new DID and publish the associated DIDDoc on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/sdk/veramo-sdk-for-cheqd/setup.md) for Veramo CLI

## Instructions

### 1. Create a DID

Follow [this](../did-operations/) tutorial to generate keys and create a DID.

### 2. Create a DID-Linked Resource

Follow [this](create-a-resource.md) tutorial to create the first version of the resource.

### 3. Publish the new Resource version

Follow exactly the same steps as the first resource. Note that to create a new resource version which belongs to the same set as a previous resource, in the you **must specify the same**:

* **collectionId**
* **name**
* **resourceType**

Each resource in such a set has a link to the previous version (except the first version) and the next version (if it's not the most recent version).&#x20;

New versions have dedicated unique IDs and can be referenced and retrieved as any other resources.

#### Steps

Follow [the same](create-a-resource.md) tutorial as for resource creation, but use the same `collectionId,` `name` and `resourceType` as for the previous Resource version.
