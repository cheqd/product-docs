---
description: Create a DID-Linked Resource (DLR) on cheqd using the Credo Agent.
---

# Create DID-Linked Resource

The `createResource` method from the **Credo cheqd module** allows you to create a **DID-Linked Resource** and publish it to the cheqd network under a `did:cheqd` identifier.

DID-Linked Resources are uniquely identified by a **UUIDv4 resource ID** and persistently attached to the DID on-chain. These resources can later be resolved by ID or query parameters (e.g., `resourceName`, `resourceType`).

#### Required Parameters

| Field          | Required | Description                                                          |
| -------------- | -------- | -------------------------------------------------------------------- |
| `did`          | ✅        | The `did:cheqd` identifier under which the resource is created       |
| `id`           | ✅        | A UUID string that acts as the permanent resource ID                 |
| `name`         | ✅        | A human-readable resource name                                       |
| `resourceType` | ✅        | A short type identifier (e.g. `TrustRegistry`, `Logo`, `StatusList`) |
| `data`         | ✅        | The content to store (object, Base64-encoded string, or plain text)  |
| `version`      | ❌        | Optional semantic version string (e.g. `"1.0"`)                      |
| `alsoKnownAs`  | ❌        | Optional array of aliases for the resource                           |

#### Example

```ts
await agent.modules.cheqd.createResource(
  'did:cheqd:testnet:92874297-d824-40ea-8ae5-364a1ec9237d',
  {
    id: '6de33634-6439-4e46-aa3f-bfe03606b000',
    name: 'exampleTrustRegistry',
    resourceType: 'TrustRegistry',
    version: '1.0',
    data: {
      name: 'Example Org',
      jurisdiction: 'EU',
    },
  }
)
```

> 💡 The `id` field must be a **UUIDv4**. You are responsible for generating and tracking this ID.

### Recommendations

* Ensure the `id` is a **UUIDv4**, generated using a reliable UUID library (e.g., `uuid` in Node.js)
* Keep `resourceName` and `resourceType` descriptive but concise — they are used for resolution
* If the resource data is sensitive or large, consider encoding as a base64 string
* Use `version` to manage changes to the resource over time
