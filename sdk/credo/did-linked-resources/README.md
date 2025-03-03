# DID-Linked Resources

**"DID-Linked Resources"** are identified with a [`did:cheqd` Decentralized Identifier](https://docs.cheqd.io/product/architecture/adr-list/adr-002-did-linked-resources) with a [Universally Unique Identifier (UUID)](https://www.uuidgenerator.net/) that acts as a permanently-accessible link to fetch the resources from the cheqd ledger. We refer to this as the "**resource ID**". Through the "**resource ID**" or a set of DID URL query parameters, applications are able to persistently access a digital resource on the cheqd network.

## Create Resource[​](https://credo.js.org/guides/tutorials/cheqd#create-resource) <a href="#create-resource" id="create-resource"></a>

Using the createResource cheqd api agents are able to create custom DID-Linked Resources, including:

* Trust Registries
* Status Lists
* Logos associated with DIDs
* Governance files

### **Parameters**[**​**](https://credo.js.org/guides/tutorials/cheqd#parameters-3)

1. `did`\*
2. `name`\*
3. `resourceType`\*
4. `id`\*
5. `data`\* : Text, Base64 string or an object
6. `alsoKnownAs`
7. `version`

```typescript
await agent.modules.cheqd.createResource('did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d', {
  name: 'resourceName',
  resourceType: 'resourceType',
  id: '6de33634-6439-4e46-aa3f-bfe03606b000',
  version: '1.0',
  data: {
    name: 'name',
    age: 18,
  },
})
```

## Resolve Resource[​](https://credo.js.org/guides/tutorials/cheqd#resolve-resource) <a href="#resolve-resource" id="resolve-resource"></a>

The resolveResource function in the cheqd API allows agents to resolve DID-Linked Resources in two ways:

* By `resourceId`
* By `resourceName` and `resourceType`

Additionally, both conventions support the following query parameters:

* To fetch a specific resource version: `resourceUrl?version=<versionId>`
* To fetch the closest resource version for a given time: `resourceUrl?resourceVersionTime=<epoch time>`

### **Using `resourceId`**[**​**](https://credo.js.org/guides/tutorials/cheqd#using-resourceid)

```typescript
await agent.modules.cheqd.resolveResource(
  'did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d/resources/6de33634-6439-4e46-aa3f-bfe03606b000'
)
```

### **Using `resourceName` and `resourceType`**[**​**](https://credo.js.org/guides/tutorials/cheqd#using-resourcename-and-resourcetype)

```typescript
await agent.modules.cheqd.resolveResource(
  'did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d?resourceName=resourceName&resourceType=resourceType'
)
```



## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Understanding DLRs</strong></mark></td><td>More details about DID-Linked Resources on Cheqd.</td><td></td><td><a href="../../../studio/did-linked-resources/understanding-dlrs/">understanding-dlrs</a></td></tr><tr><td><mark style="color:blue;"><strong>Understanding AnonCreds</strong></mark></td><td>More details about AnonCreds Object Method.</td><td></td><td><a href="../../../advanced/anoncreds/">anoncreds</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Schema</strong></mark></td><td>Create an AnonCreds Schema.</td><td></td><td><a href="create-anoncreds-schema.md">create-anoncreds-schema.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Credential Definition</strong></mark></td><td>Create an AnonCreds Credential Definition</td><td></td><td><a href="create-anoncreds-credential-definition.md">create-anoncreds-credential-definition.md</a></td></tr></tbody></table>

