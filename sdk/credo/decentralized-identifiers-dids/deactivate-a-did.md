---
description: Deactivate a did:cheqd from Credo Agent
---

# Deactivate a DID

Follow these instructions to deactivate a did:cheqd DID from Credo Agent

## Deactivate DID

A DID can be deactivated, it can still be resolved

### **Parameters**[**​**](https://credo.js.org/guides/tutorials/cheqd#parameters-2)

1. `did`\*
2. `options`

```typescript
await agent.dids.deactivate({
  did: 'did:cheqd:testnet:b84817b8-43ee-4483-98c5-f03760816411',
  // an optional versionId parameter
  options: {
    versionId: '3.0',
  },
})
```
