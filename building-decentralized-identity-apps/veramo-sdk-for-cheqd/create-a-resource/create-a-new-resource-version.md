# Create a new resource version

Follow these instructions to create a new DID and publish the associated DIDDoc on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../../veramo-sdk-for-cheqd/setup-cli.md) for Veramo CLI

## Instructions

### 1. Create a DID

Follow [this](../../../veramo-sdk-for-cheqd/did-operations/) tutorial to generate keys and create a DID.

### 2. Create a Resource

Follow [this](./) tutorial to create the first version of the resource.

### 3. Publish the new Resource version

#### About versioning

Resources with the same Collection ID and name are grouped into version sets. Each resource in such a set has a link to the previous version (except the first version) and the next version (if it's not the most recent version).

To create a resource and mark it as a new version within a particular group, we need to use the same `collection-id, name` and `type` as in the previous version. Links between versions will be created automatically.

New versions have dedicated unique IDs and can be referenced and retrieved as any other resources.

#### Steps

Follow [the same](./) tutorial as for resource creation, but use the same `collection-id` and `name` as for the previous Resource version.
