# DID Module Overview

The DID module provides comprehensive functionality for managing Decentralized Identifiers (DIDs) on the cheqd network. This module enables you to create, update, and deactivate DIDs using W3C DID standards with cheqd-specific extensions.

## Summary

Decentralized Identifiers (DIDs) are a new type of identifier that enables verifiable, decentralized digital identity. The cheqd DID module supports the full lifecycle management of DIDs with the following capabilities:

### Key Features

- **Standards Compliant**: Full W3C DID Core 1.0 specification compliance
- **Multiple Key Types**: Support for Ed25519, ECDSA secp256k1, and RSA keys
- **Verification Methods**: Flexible verification method management
- **Service Endpoints**: Add and manage service endpoints for DIDs
- **Version Control**: Track DID document versions and updates
- **Deactivation**: Secure DID deactivation with proof requirements

### Supported Operations

The DID module supports three main operation categories:

1. **DID Creation**: Create new DID documents with verification methods and services
2. **DID Management**: Update existing DIDs by modifying verification methods, services, or controllers
3. **DID Deactivation**: Permanently deactivate DIDs while maintaining historical records

## Quick Start Guides

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create DID</strong></mark></td><td>Step-by-step guide to create new Decentralized Identifiers with verification methods, service endpoints, and fee abstraction support for USDC, EURe, and OSMO payments.</td><td><a href="create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Manage DID</strong></mark></td><td>Complete DID lifecycle management including updates, verification method changes, service endpoint modifications, and secure deactivation procedures.</td><td><a href="manage-did.md">manage-did.md</a></td></tr></tbody></table>

## DID Operations Reference

### Creation Operations

| Operation | Method | Description | Required Parameters | Optional Parameters |
|-----------|--------|-------------|-------------------|-------------------|
| **Create DID** | `createDidDocTx()` | Create a new DID document | `signInputs`, `didPayload`, `feePayer` | `fee`, `memo` |

### Management Operations

| Operation | Method | Description | Required Parameters | Optional Parameters |
|-----------|--------|-------------|-------------------|-------------------|
| **Update DID** | `updateDidDocTx()` | Update existing DID document | `signInputs`, `didPayload`, `feePayer` | `fee`, `memo` |
| **Add Verification Method** | `updateDidDocTx()` | Add new verification method to DID | `signInputs`, `updatedPayload`, `feePayer` | `fee`, `memo` |
| **Remove Verification Method** | `updateDidDocTx()` | Remove verification method from DID | `signInputs`, `updatedPayload`, `feePayer` | `fee`, `memo` |
| **Add Service Endpoint** | `updateDidDocTx()` | Add service endpoint to DID | `signInputs`, `updatedPayload`, `feePayer` | `fee`, `memo` |
| **Remove Service Endpoint** | `updateDidDocTx()` | Remove service endpoint from DID | `signInputs`, `updatedPayload`, `feePayer` | `fee`, `memo` |
| **Update Controller** | `updateDidDocTx()` | Change DID controller | `signInputs`, `updatedPayload`, `feePayer` | `fee`, `memo` |

### Deactivation Operations

| Operation | Method | Description | Required Parameters | Optional Parameters |
|-----------|--------|-------------|-------------------|-------------------|
| **Deactivate DID** | `deactivateDidDocTx()` | Permanently deactivate DID | `signInputs`, `didPayload`, `feePayer` | `fee`, `memo` |

## Parameter Details

### Required Parameters

#### `signInputs: ISignInputs[]`

Array of signing inputs containing:

- `verificationMethodId`: ID of the verification method used for signing
- `privateKeyHex`: Private key in hexadecimal format for signing
- `keyType` (optional): Type of cryptographic key (Ed25519, secp256k1, RSA)

#### `didPayload: DidPayload | MsgCreateDidPayload`

DID document payload containing:

- `id`: Unique DID identifier
- `verificationMethod`: Array of verification methods
- `authentication`: Array of authentication method references
- `controller`: DID controller(s)
- `service` (optional): Array of service endpoints

#### `feePayer: string`

The account address that will pay transaction fees

### Optional Parameters

#### `fee: StdFee`

Custom fee configuration:

- `amount`: Array of coin amounts for fees
- `gas`: Gas limit for the transaction
- `payer`: Fee payer address (if different from default)

#### `memo: string`

Optional transaction memo for additional context

## Usage Examples

### Creating a DID

```typescript
import { 
  createDidPayload,
  createDidVerificationMethod,
  createKeyPairBase64,
  createVerificationKeys,
  MethodSpecificIdAlgo,
  VerificationMethods,
  type ISignInputs
} from '@cheqd/sdk';

async function createDID() {
  // Generate key pair
  const keyPair = createKeyPairBase64();
  
  // Create verification keys
  const verificationKeys = createVerificationKeys(
    keyPair.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-1'
  );
  
  // Create verification methods
  const verificationMethods = createDidVerificationMethod(
    [VerificationMethods.Ed255192020],
    [verificationKeys]
  );
  
  // Create DID payload
  const didPayload = createDidPayload(verificationMethods, [verificationKeys]);
  
  // Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: didPayload.verificationMethod![0].id,
      privateKeyHex: toString(fromString(keyPair.privateKey, 'base64'), 'hex'),
    },
  ];
  
  // Create DID
  const result = await sdk.createDidDocTx(
    signInputs,
    didPayload,
    feePayer
  );
  
  return result;
}
```

### Updating a DID

```typescript
async function updateDID(existingDidPayload: any) {
  // Add new verification method
  const newKeyPair = createKeyPairBase64();
  const newVerificationKeys = createVerificationKeys(
    newKeyPair.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-2'
  );
  const newVerificationMethods = createDidVerificationMethod(
    [VerificationMethods.Ed255192020],
    [newVerificationKeys]
  );
  
  // Create updated payload
  const updatedPayload = {
    ...existingDidPayload,
    verificationMethod: [
      ...existingDidPayload.verificationMethod,
      ...newVerificationMethods
    ],
    authentication: [
      ...existingDidPayload.authentication,
      newVerificationMethods[0].id
    ]
  };
  
  // Sign with existing key
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: existingDidPayload.verificationMethod[0].id,
      privateKeyHex: 'existing-private-key-hex',
    },
  ];
  
  // Update DID
  const result = await sdk.updateDidDocTx(
    signInputs,
    updatedPayload,
    feePayer
  );
  
  return result;
}
```

### Deactivating a DID

```typescript
async function deactivateDID(didPayload: any) {
  // Create deactivation payload
  const deactivationPayload = {
    ...didPayload,
    // DID becomes inactive but historical records remain
  };
  
  // Sign with current controller key
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: didPayload.verificationMethod[0].id,
      privateKeyHex: 'controller-private-key-hex',
    },
  ];
  
  // Deactivate DID
  const result = await sdk.deactivateDidDocTx(
    signInputs,
    deactivationPayload,
    feePayer
  );
  
  return result;
}
```

## Transaction Fees

DID operations use a fixed pricing model independent of network congestion:

| Operation | Typical Gas Cost | Fee Range |
|-----------|-----------------|-----------|
| Create DID | ~350,000 gas | 0.35-0.5 CHEQ |
| Update DID | ~350,000 gas | 0.35-0.5 CHEQ |
| Deactivate DID | ~250,000 gas | 0.25-0.35 CHEQ |

> **Note**: Actual fees may vary based on DID document size and complexity. The cheqd network uses fixed pricing for identity operations regardless of feemarket dynamics.

## Key Types and Verification Methods

### Supported Key Types

| Key Type | Verification Method | Use Case | Security Level |
|----------|-------------------|----------|----------------|
| **Ed25519** | `Ed25519VerificationKey2020` | General purpose, lightweight | High |
| **secp256k1** | `EcdsaSecp256k1VerificationKey2019` | Bitcoin/Ethereum compatibility | High |
| **RSA** | `RsaVerificationKey2018` | Legacy system integration | Medium-High |

### Verification Method Examples

```typescript
// Ed25519 (Recommended)
const ed25519Method = {
  id: `${didId}#key-1`,
  type: 'Ed25519VerificationKey2020',
  controller: didId,
  publicKeyMultibase: 'z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK'
};

// secp256k1 (Blockchain Compatible)
const secp256k1Method = {
  id: `${didId}#key-2`,
  type: 'EcdsaSecp256k1VerificationKey2019',
  controller: didId,
  publicKeyMultibase: 'zQmWvQxTqbG2Z9HPJgG57jjwR154cKhbtJenbyYTWkjgF3e'
};
```

## Service Endpoints

Service endpoints define how to interact with the DID subject:

### Common Service Types

| Service Type | Purpose | Example Endpoint |
|-------------|---------|------------------|
| `LinkedDomains` | Domain verification | `https://example.com` |
| `CredentialRepository` | Credential storage | `https://credentials.example.com` |
| `MessagingService` | DIDComm messaging | `https://messaging.example.com` |
| `IdentityHub` | Data storage hub | `https://hub.example.com` |

### Service Endpoint Example

```typescript
const serviceEndpoint = {
  id: `${didId}#service-1`,
  type: 'LinkedDomains',
  serviceEndpoint: 'https://example.com'
};
```

## Error Handling

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `DID already exists` | Attempting to create existing DID | Use update operation instead |
| `Invalid signature` | Wrong private key or verification method | Verify signing key matches verification method |
| `DID not found` | Referencing non-existent DID | Check DID exists before operations |
| `Unauthorized` | Missing controller signature | Use controller's private key for signing |
| `Invalid format` | Malformed DID or document | Validate DID format and document structure |

### Error Handling Example

```typescript
async function handleDidOperation() {
  try {
    const result = await sdk.createDidDocTx(signInputs, didPayload, feePayer);
    
    if (result.code === 0) {
      console.log('✅ DID operation successful');
      return result;
    } else {
      console.error(`❌ Operation failed: ${result.rawLog}`);
    }
  } catch (error) {
    if (error.message.includes('DID already exists')) {
      console.log('💡 Try updating the existing DID instead');
    } else if (error.message.includes('signature')) {
      console.log('💡 Check your private key and verification method');
    } else {
      console.error('Unexpected error:', error);
    }
  }
}
```

## Best Practices

### 1. Key Management

- **Secure Storage**: Store private keys securely using hardware security modules or encrypted storage
- **Key Rotation**: Regularly rotate verification keys for enhanced security
- **Backup Strategy**: Maintain secure backups of controller keys

### 2. DID Document Design

- **Minimal Data**: Keep DIDs lean by storing minimal information on-chain
- **Service Endpoints**: Use service endpoints to reference off-chain data
- **Verification Methods**: Include multiple verification methods for redundancy

### 3. Transaction Management

- **Gas Estimation**: Estimate gas requirements based on document complexity
- **Fee Planning**: Account for fixed pricing model in fee budgets
- **Batch Operations**: Consider batching related operations when possible

### 4. Security Considerations

- **Controller Management**: Carefully manage controller keys and permissions
- **Update Authorization**: Ensure proper authorization for all DID updates
- **Deactivation Planning**: Plan deactivation procedures for key compromise scenarios

## Migration and Compatibility

### Upgrading from Legacy DIDs

If you have existing DIDs that need migration:

1. **Export Current State**: Backup existing DID documents
2. **Create New DIDs**: Use current SDK methods for new DIDs
3. **Update References**: Update all systems to reference new DIDs
4. **Deactivate Legacy**: Properly deactivate old DIDs after migration

### W3C Compliance

The cheqd DID module maintains full compliance with:

- W3C DID Core 1.0
- W3C DID Resolution
- W3C Verification Method specifications

## Related Documentation

- [DID Creation Guide](create-did.md) - Step-by-step DID creation
- [DID Update Examples](manage-did.md#did-update-examples) - Add verification methods, update services, rotate keys
- [DID Deactivation Examples](manage-did.md#did-deactivation-examples) - Basic and bulk deactivation procedures
- [Key Rotation](manage-did.md#example-3-rotate-verification-keys) - Secure key rotation procedures
- [Service Endpoint Management](manage-did.md#example-2-update-did-services) - Adding and updating DID services
- [Complete Working Examples](manage-did.md#complete-working-examples) - Full update and deactivation implementations
- [Error Handling & Validation](manage-did.md#error-handling-and-validation) - Pre-update validation and status checks
- [Best Practices](manage-did.md#best-practices) - Guidelines for secure DID management
- [Fee Abstraction](../fee-abstraction/using-in-transactions.md) - Using alternative tokens for fees

## Getting Help

For assistance with DID operations:

- Check the [cheqd SDK repository](https://github.com/cheqd/sdk) for updates
- Review [W3C DID specifications](https://w3c.github.io/did-core/) for standards
- Join the [cheqd Community Discord](https://discord.gg/cheqd) for support
- Consult the [cheqd documentation](https://docs.cheqd.io) for additional resources
