# Create DID-Linked Resources with cheqd SDK

This guide demonstrates how to create DID-Linked Resources using the cheqd SDK, with examples based on the SDK's test suite and common usage patterns. DID-Linked Resources allow you to associate data like schemas, credential definitions, status lists, and other artifacts with DIDs.

## Prerequisites

Before creating DID-Linked Resources, ensure you have:

1. [Installed and configured the cheqd SDK](../sdk-setup.md)
2. [Created a DID](../did-module/create-did.md) to link resources to
3. A funded wallet for paying transaction fees
4. Access to a cheqd network (testnet or mainnet)
5. The private keys associated with the DID's authentication methods

## SDK Initialization

First, initialize the SDK with the required modules:

### Basic SDK Setup

```typescript
import { createCheqdSDK, ResourceModule, CheqdNetwork, AbstractCheqdSDKModule } from '@cheqd/sdk';
import { DirectSecp256k1HdWallet } from '@cosmjs/proto-signing';

// Initialize wallet from mnemonic
const wallet = await DirectSecp256k1HdWallet.fromMnemonic(
  'your twelve word mnemonic phrase goes here like this example', 
  { prefix: 'cheqd' }
);

// Create SDK instance
const sdk = await createCheqdSDK({
  modules: [ResourceModule as unknown as AbstractCheqdSDKModule],
  rpcUrl: 'https://rpc.cheqd.network', // testnet
  network: CheqdNetwork.Testnet,
  wallet: wallet
});
```

### Advanced SDK Setup with Multiple Modules

```typescript
import { 
  createCheqdSDK, 
  DIDModule,
  ResourceModule, 
  FeemarketModule,
  CheqdNetwork,
  createDefaultCheqdRegistry,
  CheqdSigningStargateClient,
  CheqdQuerier,
  setupResourceExtension,
  AbstractCheqdSDKModule,
  type ICheqdSDKOptions 
} from '@cheqd/sdk';

async function initializeAdvancedSDK() {
  const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { 
    prefix: 'cheqd' 
  });
  
  const options: ICheqdSDKOptions = {
    modules: [
      DIDModule as unknown as AbstractCheqdSDKModule,
      ResourceModule as unknown as AbstractCheqdSDKModule, 
      FeemarketModule as unknown as AbstractCheqdSDKModule
    ],
    rpcUrl: 'https://rpc.cheqd.network',
    network: CheqdNetwork.Testnet,
    wallet: wallet
  };
  
  const sdk = await createCheqdSDK(options);
  
  // Initialize module instances for direct usage
  const registry = createDefaultCheqdRegistry([
    ...DIDModule.registryTypes,
    ...ResourceModule.registryTypes
  ]);
  const signer = await CheqdSigningStargateClient.connectWithSigner(
    'https://rpc.cheqd.network', 
    wallet, 
    { registry }
  );
  const querier = await CheqdQuerier.connectWithExtension(
    'https://rpc.cheqd.network', 
    setupResourceExtension
  );
  
  const resourceModule = new ResourceModule(signer, querier);
  
  return { sdk, resourceModule, wallet };
}
```

## Resource Creation Examples

### Example 1: Basic JSON Schema Resource

This example creates a JSON Schema as a DID-Linked Resource:

```typescript
import { 
  createResourcePayload,
  toString,
  fromString,
  v4 as uuidv4,
  type ISignInputs
} from '@cheqd/sdk';

async function createJSONSchemaResource() {
  // Initialize SDK (see SDK setup above)
  const { resourceModule, wallet } = await initializeAdvancedSDK();
  
  // DID that will own this resource
  const collectionId = 'did:cheqd:testnet:your-existing-did';
  
  // Step 1: Define the JSON Schema content
  const schemaContent = {
    "$schema": "https://json-schema.org/draft/2020-12/schema",
    "$id": "https://example.com/person.schema.json",
    "title": "Person",
    "type": "object",
    "properties": {
      "firstName": {
        "type": "string",
        "description": "The person's first name."
      },
      "lastName": {
        "type": "string",
        "description": "The person's last name."
      },
      "age": {
        "description": "Age in years which must be equal to or greater than zero.",
        "type": "integer",
        "minimum": 0
      }
    },
    "required": ["firstName", "lastName"]
  };
  
  // Step 2: Convert content to bytes
  const data = new TextEncoder().encode(JSON.stringify(schemaContent, null, 2));
  
  // Step 3: Generate unique resource ID
  const resourceId = uuidv4();
  
  // Step 4: Create resource payload
  const resourcePayload = createResourcePayload({
    collectionId,
    id: resourceId,
    name: 'Person Schema',
    resourceType: 'JSONSchema',
    mediaType: 'application/json',
    data,
    version: '1.0.0',
    alsoKnownAs: [{
      uri: `https://schema-registry.example.com/schemas/${resourceId}`,
      description: 'Schema Registry Location'
    }]
  });
  
  console.log(`Created resource payload for: ${resourcePayload.header.name}`);
  
  // Step 5: Create sign inputs (must use DID controller keys)
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-did-controller-private-key-hex', // Replace with actual key
    },
  ];
  
  // Step 6: Get fee payer and calculate fees
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await ResourceModule.generateCreateResourceJsonFees(feePayer);
  
  // Step 7: Create the resource
  // Note: This can also be called as sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee)
  const resourceTx = await resourceModule.createResourceTx(
    signInputs, 
    resourcePayload, 
    feePayer, 
    fee
  );
  
  if (resourceTx.code === 0) {
    console.log('✅ JSON Schema resource created successfully!');
    console.log(`Resource ID: ${resourcePayload.header.id}`);
    console.log(`Collection ID: ${resourcePayload.header.collectionId}`);
    console.log(`Transaction hash: ${resourceTx.transactionHash}`);
    console.log(`Resource URI: ${collectionId}/resources/${resourceId}`);
    return {
      resourceId: resourcePayload.header.id,
      collectionId: resourcePayload.header.collectionId,
      transaction: resourceTx,
      resourcePayload
    };
  } else {
    throw new Error(`Failed to create resource: ${resourceTx.rawLog}`);
  }
}
```

### Example 2: Credential Definition Resource

This example creates an AnonCreds Credential Definition resource:

```typescript
async function createCredentialDefinitionResource() {
  const { resourceModule, wallet } = await initializeAdvancedSDK();
  
  // DID that will own this credential definition
  const collectionId = 'did:cheqd:testnet:issuer-did';
  
  // Credential Definition content (example structure)
  const credDefContent = {
    "issuerId": collectionId,
    "schemaId": "did:cheqd:testnet:schema-did/resources/schema-uuid",
    "type": "CL",
    "tag": "default",
    "value": {
      "primary": {
        "n": "example-primary-key-n-value...",
        "s": "example-primary-key-s-value...",
        "r": {
          "firstName": "example-r-firstName-value...",
          "lastName": "example-r-lastName-value...",
          "age": "example-r-age-value..."
        },
        "rctxt": "example-rctxt-value...",
        "z": "example-z-value..."
      },
      "revocation": {
        "g": "1 example-g-value...",
        "g_dash": "1 example-g-dash-value...",
        "h": "1 example-h-value...",
        "h0": "1 example-h0-value...",
        "h1": "1 example-h1-value...",
        "h2": "1 example-h2-value...",
        "htilde": "1 example-htilde-value...",
        "h_cap": "1 example-h-cap-value...",
        "u": "1 example-u-value...",
        "pk": "1 example-pk-value...",
        "y": "1 example-y-value..."
      }
    }
  };
  
  // Convert content to bytes
  const data = new TextEncoder().encode(JSON.stringify(credDefContent, null, 2));
  
  // Generate resource ID
  const resourceId = uuidv4();
  
  // Create resource payload
  const resourcePayload = createResourcePayload({
    collectionId,
    id: resourceId,
    name: 'Person Credential Definition',
    resourceType: 'CL_SCHEMA',
    mediaType: 'application/json',
    data,
    version: '1.0.0',
    alsoKnownAs: [{
      uri: `https://ledger.example.com/cred-def/${resourceId}`,
      description: 'Credential Definition Registry'
    }]
  });
  
  // Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-issuer-did-private-key-hex',
    },
  ];
  
  // Execute transaction
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await ResourceModule.generateCreateResourceJsonFees(feePayer);
  // Note: This can also be called as sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee)
  const resourceTx = await resourceModule.createResourceTx(signInputs, resourcePayload, feePayer, fee);
  
  console.log(`Credential Definition resource created: ${resourcePayload.header.id}`);
  return { resourcePayload, resourceTx };
}
```

### Example 3: Bitstring Status List Resource

This example creates a Bitstring Status List for credential status management:

```typescript
async function createBitstringStatusListResource() {
  const { resourceModule, wallet } = await initializeAdvancedSDK();
  
  // DID that will manage the status list
  const collectionId = 'did:cheqd:testnet:status-list-manager-did';
  
  // Bitstring Status List content
  const statusListContent = {
    "@context": [
      "https://www.w3.org/2018/credentials/v1",
      "https://w3id.org/vc/status-list/2021/v1"
    ],
    "id": `${collectionId}/resources/bitstring-status-${uuidv4()}`,
    "type": ["VerifiableCredential", "BitstringStatusListCredential"],
    "issuer": collectionId,
    "issuanceDate": new Date().toISOString(),
    "credentialSubject": {
      "id": `${collectionId}/resources/bitstring-status-${uuidv4()}#list`,
      "type": "BitstringStatusList",
      "statusPurpose": "revocation",
      "encodedList": "H4sIAAAAAAAAA-3BMQEAAADCoPVPbQwfoAAAAAAAAAAAAAAAAAAAAIC3AYbSVKsAQAAA", // Compressed bitstring
      "statusSize": 1,
      "statusReference": "https://w3id.org/vc/status-list#BitstringStatusListEntry"
    },
    "proof": {
      "type": "Ed25519Signature2020",
      "created": new Date().toISOString(),
      "verificationMethod": `${collectionId}#key-1`,
      "proofPurpose": "assertionMethod",
      "proofValue": "z5example-proof-value-would-be-generated-by-signing-process"
    }
  };
  
  // Convert content to bytes
  const data = new TextEncoder().encode(JSON.stringify(statusListContent, null, 2));
  
  // Generate resource ID
  const resourceId = uuidv4();
  
  // Create resource payload
  const resourcePayload = createResourcePayload({
    collectionId,
    id: resourceId,
    name: 'Bitstring Status List for Revocation',
    resourceType: 'BitstringStatusList',
    mediaType: 'application/json',
    data,
    version: '1.0.0',
    alsoKnownAs: [{
      uri: `https://status.example.com/bitstring/${resourceId}`,
      description: 'Public Bitstring Status List Endpoint'
    }]
  });
  
  // Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-status-list-manager-private-key-hex',
    },
  ];
  
  // Execute transaction
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await ResourceModule.generateCreateResourceJsonFees(feePayer);
  // Note: This can also be called as sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee)
  const resourceTx = await resourceModule.createResourceTx(signInputs, resourcePayload, feePayer, fee);
  
  console.log(`Bitstring Status List resource created: ${resourcePayload.header.id}`);
  return { resourcePayload, resourceTx };
}
```

### Example 4: Image/Logo Resource

This example creates an image resource (e.g., organization logo):

```typescript
import { readFileSync } from 'fs';

async function createImageResource() {
  const { resourceModule, wallet } = await initializeAdvancedSDK();
  
  // DID that will own the image
  const collectionId = 'did:cheqd:testnet:organization-did';
  
  // Step 1: Read image file (replace with your image path)
  const imagePath = './assets/organization-logo.png';
  const imageData = readFileSync(imagePath);
  
  // Step 2: Generate resource ID
  const resourceId = uuidv4();
  
  // Step 3: Create resource payload
  const resourcePayload = createResourcePayload({
    collectionId,
    id: resourceId,
    name: 'Organization Logo',
    resourceType: 'Image',
    mediaType: 'image/png',
    data: imageData,
    version: '1.0.0',
    alsoKnownAs: [{
      uri: `https://cdn.example.com/logos/${resourceId}.png`,
      description: 'CDN Logo Location'
    }]
  });
  
  console.log(`Image resource payload created: ${resourcePayload.header.name}`);
  console.log(`Image size: ${imageData.length} bytes`);
  
  // Step 4: Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-organization-did-private-key-hex',
    },
  ];
  
  // Step 5: Execute transaction
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await ResourceModule.generateCreateResourceImageFees(feePayer);
  // Note: This can also be called as sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee)
  const resourceTx = await resourceModule.createResourceTx(signInputs, resourcePayload, feePayer, fee);
  
  console.log(`Image resource created: ${resourcePayload.header.id}`);
  return { resourcePayload, resourceTx };
}
```

### Example 5: Custom Metadata Resource

This example creates a resource with custom metadata and multiple alternative URIs:

```typescript
async function createMetadataResource() {
  const { resourceModule, wallet } = await initializeAdvancedSDK();
  
  // DID that will own the metadata
  const collectionId = 'did:cheqd:testnet:entity-did';
  
  // Custom metadata content
  const metadataContent = {
    "entityType": "Organization",
    "name": "Example Corp",
    "description": "A leading example organization in the digital identity space",
    "website": "https://example.com",
    "contact": {
      "email": "contact@example.com",
      "phone": "+1-555-0123"
    },
    "jurisdiction": "Delaware, USA",
    "established": "2020-01-15",
    "categories": ["Technology", "Digital Identity", "Privacy"],
    "certifications": [
      {
        "name": "ISO 27001",
        "issuedBy": "Certification Authority",
        "validUntil": "2025-12-31"
      }
    ],
    "trustFramework": {
      "name": "Example Trust Framework",
      "version": "1.2",
      "compliance": ["GDPR", "CCPA"]
    }
  };
  
  // Convert content to bytes
  const data = new TextEncoder().encode(JSON.stringify(metadataContent, null, 2));
  
  // Generate resource ID
  const resourceId = uuidv4();
  
  // Create resource payload with multiple alternative URIs
  const resourcePayload = createResourcePayload({
    collectionId,
    id: resourceId,
    name: 'Entity Metadata',
    resourceType: 'EntityMetadata',
    mediaType: 'application/json',
    data,
    version: '2.1.0',
    alsoKnownAs: [
      {
        uri: `https://metadata.example.com/entities/${resourceId}`,
        description: 'Primary Metadata Endpoint'
      },
      {
        uri: `https://backup.example.com/metadata/${resourceId}`,
        description: 'Backup Metadata Location'
      },
      {
        uri: `ipfs://QmExampleHash${resourceId}`,
        description: 'IPFS Distributed Storage'
      }
    ]
  });
  
  // Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-entity-did-private-key-hex',
    },
  ];
  
  // Execute transaction
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await ResourceModule.generateCreateResourceJsonFees(feePayer);
  // Note: This can also be called as sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee)
  const resourceTx = await resourceModule.createResourceTx(signInputs, resourcePayload, feePayer, fee);
  
  console.log(`Metadata resource created: ${resourcePayload.header.id}`);
  console.log(`Alternative URIs: ${resourcePayload.header.alsoKnownAs?.length}`);
  
  return { resourcePayload, resourceTx };
}
```

### Example 6: Resource with Custom Version ID

This example creates a resource with a custom version identifier for tracking:

```typescript
async function createResourceWithVersionId() {
  const { resourceModule, wallet } = await initializeAdvancedSDK();
  
  // DID that will own the resource
  const collectionId = 'did:cheqd:testnet:versioned-resource-did';
  
  // Simple text content
  const textContent = {
    "title": "API Documentation",
    "version": "3.2.1",
    "content": "This is the API documentation for version 3.2.1 of our service...",
    "lastUpdated": new Date().toISOString(),
    "changelog": [
      "Added new authentication endpoints",
      "Deprecated legacy token format",
      "Enhanced error response structure"
    ]
  };
  
  // Convert content to bytes
  const data = new TextEncoder().encode(JSON.stringify(textContent, null, 2));
  
  // Generate resource and version IDs
  const resourceId = uuidv4();
  const versionId = uuidv4();
  
  // Create resource payload
  const resourcePayload = createResourcePayload({
    collectionId,
    id: resourceId,
    name: 'API Documentation v3.2.1',
    resourceType: 'Documentation',
    mediaType: 'application/json',
    data,
    version: '3.2.1'
  });
  
  // Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-documentation-did-private-key-hex',
    },
  ];
  
  // Execute transaction with custom version ID
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await ResourceModule.generateCreateResourceJsonFees(feePayer);
  // Note: This can also be called as sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee, undefined, versionId)
  const resourceTx = await resourceModule.createResourceTx(
    signInputs, 
    resourcePayload, 
    feePayer, 
    fee,
    undefined,
    versionId
  );
  
  console.log(`Resource created with version ID: ${versionId}`);
  console.log(`Resource: ${resourcePayload.header.id}`);
  
  return { resourcePayload, resourceTx, versionId };
}
```

## Complete Working Example

Here's a complete example you can run:

```typescript
import { 
  createCheqdSDK, 
  ResourceModule,
  CheqdNetwork,
  AbstractCheqdSDKModule,
  createResourcePayload,
  v4 as uuidv4,
  type ISignInputs
} from '@cheqd/sdk';
import { DirectSecp256k1HdWallet } from '@cosmjs/proto-signing';

async function main() {
  try {
    // Replace with your mnemonic
    const mnemonic = 'abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon about';
    
    // Initialize wallet
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { 
      prefix: 'cheqd' 
    });
    
    // Create SDK instance
    const sdk = await createCheqdSDK({
      modules: [ResourceModule as unknown as AbstractCheqdSDKModule],
      rpcUrl: 'https://rpc.cheqd.network', // testnet
      network: CheqdNetwork.Testnet,
      wallet: wallet
    });
    
    // DID that will own the resource (replace with your DID)
    const collectionId = 'did:cheqd:testnet:your-existing-did';
    
    // Create simple schema content
    const schemaContent = {
      "$schema": "https://json-schema.org/draft/2020-12/schema",
      "title": "Simple Example Schema",
      "type": "object",
      "properties": {
        "name": { "type": "string" },
        "email": { "type": "string", "format": "email" }
      },
      "required": ["name", "email"]
    };
    
    console.log(`Creating resource for DID: ${collectionId}`);
    
    // Convert content to bytes
    const data = new TextEncoder().encode(JSON.stringify(schemaContent, null, 2));
    
    // Generate resource ID
    const resourceId = uuidv4();
    console.log(`Generated resource ID: ${resourceId}`);
    
    // Create resource payload
    const resourcePayload = createResourcePayload({
      collectionId,
      id: resourceId,
      name: 'Simple Example Schema',
      resourceType: 'JSONSchema',
      mediaType: 'application/json',
      data,
      version: '1.0.0'
    });
    console.log(`Created resource payload`);
    
    // Create sign inputs
    const signInputs: ISignInputs[] = [
      {
        verificationMethodId: `${collectionId}#key-1`, // Adjust based on your DID
        privateKeyHex: 'your-did-private-key-hex', // Replace with your actual private key
      },
    ];
    
    // Get fee payer
    const feePayer = (await wallet.getAccounts())[0].address;
    console.log(`Fee payer: ${feePayer}`);
    
    // Calculate fees
    const fee = await ResourceModule.generateCreateResourceJsonFees(feePayer);
    console.log(`Calculated fee: ${JSON.stringify(fee)}`);
    
    // Create resource using SDK dot notation
    const result = await sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee);
    
    if (result.code === 0) {
      console.log('✅ Resource created successfully!');
      console.log(`🆔 Resource ID: ${resourcePayload.header.id}`);
      console.log(`🔗 Collection ID: ${resourcePayload.header.collectionId}`);
      console.log(`📄 Transaction hash: ${result.transactionHash}`);
      console.log(`⛽ Gas used: ${result.gasUsed}`);
      console.log(`🌐 Resource URI: ${collectionId}/resources/${resourceId}`);
    } else {
      console.error(`❌ Failed to create resource: ${result.rawLog}`);
    }
    
  } catch (error) {
    console.error('Error creating resource:', error);
  }
}

// Run the example
main();
```

## Error Handling and Validation

### Resource Payload Validation

Before submitting to the ledger, validate your resource payload:

```typescript
import { validateResourcePayload } from '@cheqd/sdk';

// Validate resource payload before submission
const validation = validateResourcePayload(resourcePayload);
if (!validation.valid) {
  throw new Error(`Invalid resource payload: ${validation.error}`);
}
console.log('✅ Resource payload validation passed');
```

### Content Size Limits

Check resource content size limits:

```typescript
const MAX_RESOURCE_SIZE = 200 * 1024; // 200KB limit (example)

if (data.length > MAX_RESOURCE_SIZE) {
  throw new Error(`Resource too large: ${data.length} bytes (max: ${MAX_RESOURCE_SIZE} bytes)`);
}
console.log(`✅ Resource size OK: ${data.length} bytes`);
```

### Fee Calculation by Media Type

The SDK provides specific fee calculation methods based on the resource media type:

```typescript
// For JSON-based resources (schemas, metadata, etc.)
const jsonFee = await ResourceModule.generateCreateResourceJsonFees(feePayer);

// For image resources (PNG, JPEG, SVG, etc.)
const imageFee = await ResourceModule.generateCreateResourceImageFees(feePayer);

// For other/unknown media types
const defaultFee = await ResourceModule.generateCreateResourceDefaultFees(feePayer);

// Choose the appropriate method based on your resource type
const fee = resourcePayload.header.mediaType.startsWith('application/json') 
  ? await ResourceModule.generateCreateResourceJsonFees(feePayer)
  : resourcePayload.header.mediaType.startsWith('image/')
  ? await ResourceModule.generateCreateResourceImageFees(feePayer)
  : await ResourceModule.generateCreateResourceDefaultFees(feePayer);
```

### DID Authorization Check

Verify that the signing keys have authority over the collection DID:

```typescript
// Query the collection DID to verify authorization
const collectionDidDoc = await sdk.queryDidDoc(collectionId);
if (!collectionDidDoc.didDoc) {
  throw new Error(`Collection DID not found: ${collectionId}`);
}

// Check if verification method exists in the DID
const verificationMethodExists = collectionDidDoc.didDoc.verificationMethod?.some(
  vm => vm.id === signInputs[0].verificationMethodId
);

if (!verificationMethodExists) {
  throw new Error(`Verification method not found in collection DID: ${signInputs[0].verificationMethodId}`);
}
console.log('✅ Authorization check passed');
```

## Best Practices

### Resource Design

1. **Meaningful Names**: Use descriptive names that clearly identify the resource
2. **Version Management**: Use semantic versioning for resource versions
3. **Media Types**: Use appropriate MIME types for your content
4. **Resource Types**: Use standardized resource type names when possible
5. **Size Optimization**: Keep resources reasonably sized for network efficiency

### Security Considerations

1. **Key Management**: Secure storage of private keys used for signing
2. **Content Validation**: Validate content before creating resources
3. **Access Control**: Ensure only authorized entities can create resources for a DID
4. **Backup Strategies**: Maintain backups of critical resources
5. **Version Control**: Track resource changes and maintain audit trails

### Performance Optimization

1. **Batch Operations**: Group related resource creations when possible
2. **Content Compression**: Compress large text-based resources
3. **Caching**: Implement caching strategies for frequently accessed resources
4. **CDN Integration**: Use Content Delivery Networks for global distribution
5. **Alternative URIs**: Provide multiple access methods through `alsoKnownAs`

## Common Resource Types

### Standard Resource Types

- `JSONSchema` - JSON Schema definitions
- `CL_SCHEMA` - AnonCreds credential definitions
- `BitstringStatusList` - W3C Bitstring Status List for credential status
- `Image` - Images, logos, and graphics
- `Documentation` - API docs, specifications, guides
- `EntityMetadata` - Organization or entity information
- `TermsOfService` - Legal terms and conditions
- `PrivacyPolicy` - Privacy policies and data handling terms

### Custom Resource Types

You can create custom resource types for specific use cases:

```typescript
// Example custom resource types
const customResourceTypes = [
  'APISpecification',
  'ComplianceCertificate',
  'BusinessLicense',
  'ProductCatalog',
  'PricingModel',
  'ServiceAgreement'
];
```

## Next Steps

After creating resources, you can:

- [Update DID-Linked Resources](manage-resource.md)
- Implement resource versioning strategies

## Troubleshooting

### Common Issues

1. **DID Not Found**: Verify the collection DID exists and is active
2. **Insufficient Permissions**: Ensure signing keys have authority over the collection DID
3. **Resource Too Large**: Check resource size limits and compress if needed
4. **Invalid Media Type**: Use proper MIME types for your content
5. **Network Errors**: Verify connection to the cheqd network
6. **Fee Calculation Errors**: Ensure wallet has sufficient funds

### Getting Help

If you encounter issues:

- Check the [cheqd SDK GitHub repository](https://github.com/cheqd/sdk) for updates
- Review the [test files](https://github.com/cheqd/sdk/tree/main/esm/tests) for examples
- Consult the [cheqd documentation](https://docs.cheqd.io/) for comprehensive guides
- Join the [cheqd Community Discord](https://discord.gg/cheqd) for support
