# DID-Linked Resource Module Overview

The DID-Linked Resource module provides comprehensive functionality for managing DID-Linked Resources on the cheqd network. This module enables you to create, version, and manage immutable resources that are cryptographically linked to DIDs, supporting various data types like schemas, credential definitions, status lists, and custom artifacts.

## Summary

DID-Linked Resources are immutable data objects that can be linked to DIDs, providing a secure and verifiable way to associate additional data with Decentralized Identifiers. The cheqd DID-Linked Resource module supports comprehensive resource lifecycle management with the following capabilities:

### Key Features

- **Immutable Storage**: Resources are permanently stored and cannot be modified after creation
- **Version Management**: Create new versions of resources with automatic double-linked list versioning
- **Multiple Resource Types**: Support for schemas, credential definitions, status lists, and custom types
- **Cryptographic Linking**: Resources are cryptographically linked to DIDs for authenticity
- **Content Addressing**: Unique resource identification using checksums and metadata
- **Fee Abstraction**: Pay fees using IBC tokens (USDC, EURe, OSMO) or native CHEQ tokens

### Supported Operations

The DID-Linked Resource module supports two main operation categories:

1. **Resource Creation**: Create new immutable resources linked to DIDs
2. **Resource Management**: Create new versions of existing resources (versioning system)

## Quick Start Guides

<!-- markdownlint-disable MD033 -->
<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create Resource</strong></mark></td><td>Step-by-step guide to create new DID-Linked Resources including schemas, credential definitions, status lists, and custom data with fee abstraction support.</td><td><a href="create-resource.md">create-resource.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Manage Resource</strong></mark></td><td>Complete resource lifecycle management including versioning, double-linked list management, and understanding immutable resource patterns.</td><td><a href="manage-resource.md">manage-resource.md</a></td></tr></tbody></table>
<!-- markdownlint-enable MD033 -->

## Resource Operations Reference

### Creation Operations

| Operation | Method | Description | Required Parameters | Optional Parameters |
|-----------|--------|-------------|-------------------|-------------------|
| **Create Resource** | `createLinkedResourceTx()` | Create a new DID-Linked Resource | `signInputs`, `resourcePayload`, `feePayer` | `fee`, `memo` |

### Management Operations

| Operation | Method | Description | Required Parameters | Optional Parameters |
|-----------|--------|-------------|-------------------|-------------------|
| **Create Version** | `createLinkedResourceTx()` | Create new version of existing resource | `signInputs`, `resourcePayload`, `feePayer` | `fee`, `memo` |

## Resource Types and Use Cases

### Supported Resource Types

| Resource Type | Description | Common Use Cases | Content Format |
|--------------|-------------|------------------|----------------|
| **CL-Schema** | Camenisch-Lysyanskaya Schema | AnonCreds credential schemas | JSON schema definition |
| **CL-CredDef** | Credential Definition | AnonCreds credential definitions | JSON credential definition |
| **Bitstring Status List** | Revocation Status List | Credential revocation/suspension tracking | Compressed bitstring |
| **JSONSchema** | JSON Schema | W3C VC JSON Schema validation | JSON schema specification |
| **custom** | Custom Resource Type | Application-specific data | Any format (JSON, text, binary) |

### Resource Metadata

Each DID-Linked Resource includes comprehensive metadata:

| Field | Type | Description | Required |
|-------|------|-------------|----------|
| `collectionId` | string | DID that owns the resource | ✅ |
| `id` | string | Unique resource identifier (UUID) | ✅ |
| `name` | string | Human-readable resource name | ✅ |
| `resourceType` | string | Type of resource (schema, credDef, etc.) | ✅ |
| `mediaType` | string | MIME type of resource content | ✅ |
| `checksum` | string | SHA-256 hash of resource content | ✅ |
| `created` | string | ISO 8601 creation timestamp | ✅ |
| `version` | string | Resource version identifier | ❌ |
| `alsoKnownAs` | array | Alternative resource identifiers | ❌ |
| `nextVersionId` | string | Next version in the chain | ❌ |
| `previousVersionId` | string | Previous version in the chain | ❌ |

## Versioning and Immutability

### Version Chain Management

DID-Linked Resources use a double-linked list structure for versioning:

```text
Version 1 ←→ Version 2 ←→ Version 3
    ↑           ↑           ↑
  oldest    intermediate   newest
```

### Key Versioning Rules

1. **Same Name + Type = New Version**: Resources with identical `name` and `resourceType` create version chains
2. **Automatic Linking**: New versions automatically link to previous versions
3. **Immutable History**: All versions remain permanently accessible
4. **Content Independence**: Versions can have identical or different content

## Fee Structure and Payment Options

### Fee Payment Methods

| Payment Method | Token Types | Use Cases |
|---------------|-------------|-----------|
| **Native Fees** | CHEQ | Standard network fees |
| **Fee Abstraction** | USDC, EURe, OSMO | IBC token fee payment |

### Resource Creation Costs

Resource creation fees are based on:

- Base transaction fee (network operation)
- Storage fee (proportional to resource size)
- Additional validation costs (for complex resource types)

## Integration Patterns

### Common Workflows

| Pattern | Description | Implementation |
|---------|-------------|----------------|
| **Schema Publishing** | Publish credential schemas for verifiers | Create CL-Schema or JSONSchema resources |
| **Credential Definition** | Define credential parameters for issuers | Create CL-CredDef resources linked to schemas |
| **Status List Management** | Track credential revocation status | Create and version StatusList2021 resources |
| **Custom Data Storage** | Store application-specific data | Create custom resource types with arbitrary content |

### Best Practices

1. **Resource Planning**: Design resource naming and versioning strategy before creation
2. **Content Validation**: Validate resource content before creation (immutable after creation)
3. **Version Management**: Use descriptive version identifiers for easier tracking
4. **Fee Optimization**: Choose appropriate token types based on cost and availability
5. **Access Patterns**: Consider how applications will discover and retrieve resources

## Error Handling

### Common Error Scenarios

| Error Type | Cause | Resolution |
|------------|-------|------------|
| **Invalid DID** | Collection ID doesn't exist or is deactivated | Verify DID exists and is active |
| **Insufficient Fees** | Transaction fee too low | Increase fee amount or check fee calculation |
| **Invalid Content** | Resource content doesn't match type requirements | Validate content format before creation |
| **Authorization Failed** | Signing key not authorized for DID | Use authorized verification method |
| **Network Issues** | Connection or network problems | Retry with exponential backoff |

### Error Response Format

```typescript
interface ResourceError {
  code: number;
  message: string;
  details?: {
    resource?: string;
    collection?: string;
    validation?: string[];
  };
}
```

## Advanced Usage Examples

### Programmatic Resource Discovery

```typescript
// Query resources by collection (DID)
const resources = await sdk.querier.cheqd.resource.v2.collectionResources({
  collectionId: "did:cheqd:mainnet:example123"
});

// Query specific resource with metadata
const resource = await sdk.querier.cheqd.resource.v2.resource({
  collectionId: "did:cheqd:mainnet:example123",
  id: "resource-uuid-here"
});

// Query resource version chain
const versionChain = await sdk.querier.cheqd.resource.v2.resourceVersions({
  collectionId: "did:cheqd:mainnet:example123",
  name: "CredentialSchema",
  resourceType: "CL-Schema"
});
```

### Resource Content Retrieval

```typescript
// Fetch resource content by ID
const content = await fetch(
  `https://resolver.cheqd.net/1.0/identifiers/${collectionId}/resources/${resourceId}`
);

// Fetch latest version by name and type
const latestContent = await fetch(
  `https://resolver.cheqd.net/1.0/identifiers/${collectionId}/resources/${name}?resourceType=${type}`
);
```

## Migration and Compatibility

### SDK Version Compatibility

| SDK Version | Resource Module Features | Breaking Changes |
|-------------|--------------------------|------------------|
| **v3.0+** | Full versioning support, fee abstraction | Updated method signatures |
| **v2.x** | Basic resource creation | Limited versioning |
| **v1.x** | Legacy resource format | Not recommended |

### Migration Guidelines

When upgrading from older SDK versions:

1. **Update Dependencies**: Upgrade to latest SDK version
2. **Review Method Signatures**: Check for parameter changes
3. **Test Resource Creation**: Validate new version behavior
4. **Update Fee Handling**: Implement fee abstraction if needed

## Security Considerations

### Resource Security Best Practices

1. **Content Validation**: Always validate resource content before creation
2. **Key Management**: Secure storage of DID authentication keys
3. **Access Control**: Implement proper authorization checks
4. **Content Verification**: Verify resource checksums after retrieval
5. **Network Security**: Use HTTPS endpoints for resource access

### Privacy Considerations

1. **Public Storage**: All resources are publicly accessible
2. **Content Sensitivity**: Avoid storing sensitive personal data
3. **Metadata Privacy**: Resource names and types are public
4. **Version History**: All versions remain permanently visible

## Troubleshooting

### Common Issues

| Issue | Symptoms | Solution |
|-------|----------|----------|
| **Resource Not Found** | 404 errors when accessing resources | Verify collection ID and resource ID |
| **Version Chain Broken** | Missing version links | Check version creation order |
| **Fee Calculation Errors** | Transaction rejected due to insufficient fees | Use dynamic fee calculation |
| **Content Type Mismatch** | Resource rejected during creation | Ensure content matches declared mediaType |
| **Authorization Errors** | Transaction fails with authorization error | Verify signing key is authorized for DID |

### Debug Information

For debugging resource operations:

1. **Check Network Status**: Verify network connectivity and status
2. **Validate Input Parameters**: Ensure all required parameters are provided
3. **Review Transaction Logs**: Check transaction hash and block explorer
4. **Verify DID Status**: Confirm the associated DID is active
5. **Check Resource Limits**: Verify resource size is within network limits

## Related Documentation

### SDK Setup and Configuration

- [SDK Setup Guide](../sdk-setup.md) - Initial SDK configuration
- [Fee Abstraction Guide](../fee-abstraction/using-in-transactions.md) - IBC token fee payment

### DID Management

- [DID Module Overview](../did-module/README.md) - DID lifecycle management
- [Create DID Guide](../did-module/create-did.md) - DID creation procedures

### Network and Tools

- [Network Overview](../../../network/cheqd/README.md) - cheqd network information
- [Block Explorer](../../../network/tools/block-explorer.md) - Transaction monitoring
