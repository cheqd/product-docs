# Manage DID-Linked Resources with cheqd SDK

This guide demonstrates how to manage DID-Linked Resources using the cheqd SDK. Unlike traditional "updating," DID-Linked Resources are **immutable** - managing them means creating new versions that form a double-linked list of resource history.

## Understanding Resource Versioning

### Key Concepts

**DID-Linked Resources are immutable.** When you "update" a resource, you're actually creating a new version with the same name and resource type. Each version in the chain contains:

- `nextVersionId` - Points to the newer version (if it exists)
- `previousVersionId` - Points to the older version (if it exists)

### Version Creation Rules

1. **New Version**: Created when `name` and `resourceType` are identical to an existing resource
   - Content can be the same or different
   - Creates a linked version in the chain
   - Gets assigned `nextVersionId` and `previousVersionId`

2. **New Resource**: Created when `name` OR `resourceType` differ from existing resources
   - Starts a completely new resource chain
   - No version linking to existing resources

## Prerequisites

Before managing resources, ensure you have:

1. [Installed and configured the cheqd SDK](../sdk-setup.md)
2. [Created a DID-Linked Resource](create-resource.md) to version
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

## Resource Versioning Examples

### Example 1: Create New Version of JSON Schema

This example creates a new version of an existing JSON Schema resource:

```typescript
import { 
  createResourcePayload,
  v4 as uuidv4,
  type ISignInputs
} from '@cheqd/sdk';

async function createSchemaVersion() {
  // Initialize SDK (see SDK setup above)
  const { resourceModule, wallet } = await initializeAdvancedSDK();
  
  // DID that owns the resource
  const collectionId = 'did:cheqd:testnet:your-existing-did';
  
  // Step 1: Query existing resource to understand current version chain
  const existingResourceId = 'existing-resource-uuid';
  const existingResource = await resourceModule.queryResource(collectionId, existingResourceId);
  
  if (!existingResource) {
    throw new Error(`Resource not found: ${existingResourceId}`);
  }
  
  console.log(`Found existing resource: ${existingResource.header.name}`);
  console.log(`Current version: ${existingResource.header.version}`);
  console.log(`Previous version ID: ${existingResource.header.previousVersionId || 'None (first version)'}`);
  
  // Step 2: Create updated schema content (version 2.0.0)
  const updatedSchemaContent = {
    "$schema": "https://json-schema.org/draft/2020-12/schema",
    "$id": "https://example.com/person.schema.json",
    "title": "Person",
    "type": "object",
    "properties": {
      "firstName": {
        "type": "string",
        "description": "The person's first name.",
        "minLength": 1
      },
      "lastName": {
        "type": "string", 
        "description": "The person's last name.",
        "minLength": 1
      },
      "age": {
        "description": "Age in years which must be equal to or greater than zero.",
        "type": "integer",
        "minimum": 0,
        "maximum": 150
      },
      // NEW FIELD in version 2.0.0
      "email": {
        "type": "string",
        "format": "email",
        "description": "Contact email address."
      }
    },
    "required": ["firstName", "lastName", "email"] // Updated requirements
  };
  
  // Step 3: Convert content to bytes
  const data = new TextEncoder().encode(JSON.stringify(updatedSchemaContent, null, 2));
  
  // Step 4: Generate new resource ID for this version
  const newVersionId = uuidv4();
  
  // Step 5: Create resource payload with SAME name and resourceType
  const resourcePayload = createResourcePayload({
    collectionId,
    id: newVersionId,
    name: existingResource.header.name, // SAME name = new version
    resourceType: existingResource.header.resourceType, // SAME type = new version
    mediaType: 'application/json',
    data,
    version: '2.0.0', // Updated version number
    previousVersionId: existingResourceId, // Link to previous version
    alsoKnownAs: [{
      uri: `https://schema-registry.example.com/schemas/${newVersionId}`,
      description: 'Schema Registry Location v2.0.0'
    }]
  });
  
  console.log(`Creating new version: ${resourcePayload.header.version}`);
  console.log(`Previous version ID: ${resourcePayload.header.previousVersionId}`);
  
  // Step 6: Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-did-controller-private-key-hex',
    },
  ];
  
  // Step 7: Create the new version
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await ResourceModule.generateCreateResourceJsonFees(feePayer);
  // Note: This can also be called as sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee)
  const versionTx = await resourceModule.createResourceTx(
    signInputs, 
    resourcePayload, 
    feePayer, 
    fee
  );
  
  if (versionTx.code === 0) {
    console.log('✅ New resource version created successfully!');
    console.log(`New Version ID: ${resourcePayload.header.id}`);
    console.log(`Version: ${resourcePayload.header.version}`);
    console.log(`Transaction hash: ${versionTx.transactionHash}`);
    
    // The previous version will now have this version's ID as its nextVersionId
    console.log(`Previous version (${existingResourceId}) now points to this version`);
    
    return {
      newVersionId: resourcePayload.header.id,
      version: resourcePayload.header.version,
      previousVersionId: existingResourceId,
      transaction: versionTx,
      resourcePayload
    };
  } else {
    throw new Error(`Failed to create resource version: ${versionTx.rawLog}`);
  }
}
```

### Example 2: Create New Version with Same Content (Timestamp Update)

This example shows creating a new version even when content hasn't changed:

```typescript
async function createTimestampVersion() {
  const { resourceModule, wallet } = await initializeAdvancedSDK();
  
  // DID that owns the resource
  const collectionId = 'did:cheqd:testnet:status-manager-did';
  
  // Query existing bitstring status list
  const existingResourceId = 'existing-status-list-uuid';
  const existingResource = await resourceModule.queryResource(collectionId, existingResourceId);
  
  // Parse existing content
  const existingContent = JSON.parse(new TextDecoder().decode(existingResource.data));
  
  // Create "updated" content - same data but new issuance timestamp
  const updatedContent = {
    ...existingContent,
    issuanceDate: new Date().toISOString(), // Only change: new timestamp
    // encodedList stays the same - no actual status changes
  };
  
  const data = new TextEncoder().encode(JSON.stringify(updatedContent, null, 2));
  const newVersionId = uuidv4();
  
  // Create resource payload - SAME name and type = new version
  const resourcePayload = createResourcePayload({
    collectionId,
    id: newVersionId,
    name: existingResource.header.name, // SAME name
    resourceType: existingResource.header.resourceType, // SAME type  
    mediaType: 'application/json',
    data,
    version: '1.0.1', // Patch version bump
    previousVersionId: existingResourceId
  });
  
  // Sign and create version
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-status-manager-private-key-hex',
    },
  ];
  
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await ResourceModule.generateCreateResourceJsonFees(feePayer);
  // Note: This can also be called as sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee)
  const versionTx = await resourceModule.createResourceTx(signInputs, resourcePayload, feePayer, fee);
  
  console.log(`Timestamp version created: ${resourcePayload.header.id}`);
  console.log(`Content unchanged, only timestamp updated`);
  
  return { resourcePayload, versionTx };
}
```

### Example 3: Create New Resource (Different Name/Type)

This example shows creating a completely new resource rather than a version:

```typescript
async function createNewResourceNotVersion() {
  const { resourceModule, wallet } = await initializeAdvancedSDK();
  
  const collectionId = 'did:cheqd:testnet:your-did';
  
  // This will create a NEW resource because either name OR type differs
  const newResourceContent = {
    "title": "Employee Schema", // DIFFERENT name from "Person Schema"
    "type": "object",
    "properties": {
      "employeeId": { "type": "string" },
      "department": { "type": "string" },
      "position": { "type": "string" }
    }
  };
  
  const data = new TextEncoder().encode(JSON.stringify(newResourceContent, null, 2));
  const resourceId = uuidv4();
  
  // Different name = NEW resource (not a version)
  const resourcePayload = createResourcePayload({
    collectionId,
    id: resourceId,
    name: 'Employee Schema', // DIFFERENT name = new resource
    resourceType: 'JSONSchema', // Same type, but name differs
    mediaType: 'application/json',
    data,
    version: '1.0.0' // Starts at v1.0.0 - no previousVersionId
  });
  
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-did-private-key-hex',
    },
  ];
  
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await ResourceModule.generateCreateResourceJsonFees(feePayer);
  // Note: This can also be called as sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee)
  const resourceTx = await resourceModule.createResourceTx(signInputs, resourcePayload, feePayer, fee);
  
  console.log(`New resource created (not a version): ${resourcePayload.header.id}`);
  console.log(`Resource name: ${resourcePayload.header.name}`);
  console.log(`No version linking - this starts a new chain`);
  
  return { resourcePayload, resourceTx };
}
```

### Example 4: Create Version with Different Resource Type

This example shows what happens when you change the resource type:

```typescript
async function attemptTypeChangeVersion() {
  const { resourceModule, wallet } = await initializeAdvancedSDK();
  
  const collectionId = 'did:cheqd:testnet:your-did';
  
  // Query existing resource
  const existingResourceId = 'existing-json-schema-uuid';
  const existingResource = await resourceModule.queryResource(collectionId, existingResourceId);
  
  // Attempt to create "version" with different resource type
  const documentationContent = {
    "title": existingResource.header.name, // SAME name
    "description": "Documentation for the Person schema",
    "lastUpdated": new Date().toISOString()
  };
  
  const data = new TextEncoder().encode(JSON.stringify(documentationContent, null, 2));
  const newResourceId = uuidv4();
  
  const resourcePayload = createResourcePayload({
    collectionId,
    id: newResourceId,
    name: existingResource.header.name, // SAME name
    resourceType: 'Documentation', // DIFFERENT type = NEW resource
    mediaType: 'application/json',
    data,
    version: '1.0.0' // Starts new chain - no previousVersionId
  });
  
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-did-private-key-hex',
    },
  ];
  
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await ResourceModule.generateCreateResourceJsonFees(feePayer);
  // Note: This can also be called as sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee)
  const resourceTx = await resourceModule.createResourceTx(signInputs, resourcePayload, feePayer, fee);
  
  console.log(`⚠️  NEW resource created (not a version due to different type)`);
  console.log(`Same name: "${resourcePayload.header.name}"`);
  console.log(`Different type: "${resourcePayload.header.resourceType}"`);
  console.log(`No version linking to existing resource`);
  
  return { resourcePayload, resourceTx };
}
```

### Example 5: Complex Version Chain Navigation

This example shows how to navigate and understand a version chain:

```typescript
async function exploreVersionChain() {
  const { resourceModule } = await initializeAdvancedSDK();
  
  const collectionId = 'did:cheqd:testnet:your-did';
  const startingResourceId = 'some-resource-uuid';
  
  // Step 1: Find the first version in the chain
  async function findFirstVersion(resourceId: string): Promise<any> {
    const resource = await resourceModule.queryResource(collectionId, resourceId);
    
    if (!resource.header.previousVersionId) {
      return resource; // This is the first version
    }
    
    // Recursively find the first version
    return findFirstVersion(resource.header.previousVersionId);
  }
  
  // Step 2: Traverse the entire chain from first to last
  async function traverseVersionChain(firstVersionId: string): Promise<any[]> {
    const versions = [];
    let currentVersionId = firstVersionId;
    
    while (currentVersionId) {
      const resource = await resourceModule.queryResource(collectionId, currentVersionId);
      versions.push(resource);
      
      currentVersionId = resource.header.nextVersionId;
    }
    
    return versions;
  }
  
  // Step 3: Get complete version history
  const firstVersion = await findFirstVersion(startingResourceId);
  const allVersions = await traverseVersionChain(firstVersion.header.id);
  
  console.log(`📜 Version Chain Analysis for "${firstVersion.header.name}"`);
  console.log(`Resource Type: ${firstVersion.header.resourceType}`);
  console.log(`Total Versions: ${allVersions.length}`);
  console.log('');
  
  allVersions.forEach((version, index) => {
    console.log(`Version ${index + 1}:`);
    console.log(`  ID: ${version.header.id}`);
    console.log(`  Version: ${version.header.version}`);
    console.log(`  Created: ${version.header.created}`);
    console.log(`  Previous: ${version.header.previousVersionId || 'None'}`);
    console.log(`  Next: ${version.header.nextVersionId || 'None (latest)'}`);
    console.log(`  Size: ${version.data.length} bytes`);
    console.log('');
  });
  
  return {
    resourceName: firstVersion.header.name,
    resourceType: firstVersion.header.resourceType,
    totalVersions: allVersions.length,
    firstVersion: allVersions[0],
    latestVersion: allVersions[allVersions.length - 1],
    allVersions
  };
}
```

## Complete Working Example

Here's a complete example for creating a resource version:

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

async function completeVersionExample() {
  try {
    // Replace with your mnemonic
    const mnemonic = 'abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon about';
    
    // Initialize wallet and SDK
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { 
      prefix: 'cheqd' 
    });
    
    const sdk = await createCheqdSDK({
      modules: [ResourceModule as unknown as AbstractCheqdSDKModule],
      rpcUrl: 'https://rpc.cheqd.network',
      network: CheqdNetwork.Testnet,
      wallet: wallet
    });
    
    // Resource information
    const collectionId = 'did:cheqd:testnet:your-existing-did';
    const existingResourceId = 'your-existing-resource-id';
    
    // Step 1: Query existing resource
    console.log(`🔍 Querying existing resource: ${existingResourceId}`);
    const existingResource = await sdk.queryResource(collectionId, existingResourceId);
    
    if (!existingResource) {
      throw new Error(`Resource not found: ${existingResourceId}`);
    }
    
    console.log(`✅ Found resource: "${existingResource.header.name}"`);
    console.log(`📋 Type: ${existingResource.header.resourceType}`);
    console.log(`📊 Current version: ${existingResource.header.version}`);
    
    // Step 2: Create updated content
    const originalContent = JSON.parse(new TextDecoder().decode(existingResource.data));
    const updatedContent = {
      ...originalContent,
      // Add version history metadata
      versionHistory: {
        previousVersion: existingResource.header.version,
        updatedAt: new Date().toISOString(),
        changes: ["Added version tracking", "Updated schema validation"]
      }
    };
    
    // Step 3: Create new version
    const data = new TextEncoder().encode(JSON.stringify(updatedContent, null, 2));
    const newVersionId = uuidv4();
    
    const resourcePayload = createResourcePayload({
      collectionId,
      id: newVersionId,
      name: existingResource.header.name, // SAME name = version
      resourceType: existingResource.header.resourceType, // SAME type = version
      mediaType: existingResource.header.mediaType,
      data,
      version: '1.1.0', // Increment version
      previousVersionId: existingResourceId
    });
    
    console.log(`🔄 Creating new version: ${resourcePayload.header.version}`);
    
    // Step 4: Sign and submit
    const signInputs: ISignInputs[] = [
      {
        verificationMethodId: `${collectionId}#key-1`,
        privateKeyHex: 'your-did-private-key-hex', // Replace with actual key
      },
    ];
    
    const feePayer = (await wallet.getAccounts())[0].address;
    const fee = await ResourceModule.generateCreateResourceJsonFees(feePayer);
    
    const result = await sdk.createResourceTx(signInputs, resourcePayload, feePayer, fee);
    
    if (result.code === 0) {
      console.log('✅ Resource version created successfully!');
      console.log(`🆔 New Version ID: ${resourcePayload.header.id}`);
      console.log(`📈 Version: ${resourcePayload.header.version}`);  
      console.log(`🔗 Previous Version: ${resourcePayload.header.previousVersionId}`);
      console.log(`📄 Transaction: ${result.transactionHash}`);
      console.log(`⛽ Gas used: ${result.gasUsed}`);
      
      // The version chain now looks like:
      // [Original] ←→ [New Version]
      //     ↑             ↑
      // nextVersionId = newVersionId
      //           previousVersionId = existingResourceId
      
    } else {
      console.error(`❌ Failed to create version: ${result.rawLog}`);
    }
    
  } catch (error) {
    console.error('Error creating resource version:', error);
  }
}

// Run the example
completeVersionExample();
```

## Version Chain Management

### Understanding the Double-Linked List

Each resource version maintains links to form a double-linked list:

```text
[Version 1] ←→ [Version 2] ←→ [Version 3] ←→ [Version 4]
     ↑              ↑              ↑              ↑
previousVersionId=null    previousVersionId=v1   previousVersionId=v2   previousVersionId=v3
nextVersionId=v2          nextVersionId=v3       nextVersionId=v4       nextVersionId=null
```

### Version Navigation Patterns

```typescript
// Get latest version using SDK
async function getLatestVersion(sdk: any, collectionId: string, anyVersionId: string) {
  let current = await sdk.queryResource(collectionId, anyVersionId);
  
  while (current.header.nextVersionId) {
    current = await sdk.queryResource(collectionId, current.header.nextVersionId);
  }
  
  return current; // This is the latest version
}

// Get first version using SDK
async function getFirstVersion(sdk: any, collectionId: string, anyVersionId: string) {
  let current = await sdk.queryResource(collectionId, anyVersionId);
  
  while (current.header.previousVersionId) {
    current = await sdk.queryResource(collectionId, current.header.previousVersionId);
  }
  
  return current; // This is the first version
}

// Get version count using SDK
async function getVersionCount(sdk: any, collectionId: string, anyVersionId: string) {
  const firstVersion = await getFirstVersion(sdk, collectionId, anyVersionId);
  let count = 1;
  let current = firstVersion;
  
  while (current.header.nextVersionId) {
    current = await sdk.queryResource(collectionId, current.header.nextVersionId);
    count++;
  }
  
  return count;
}
```

### Alternative: Using DID Resolver for Version Navigation

**Note**: Version navigation patterns can also be implemented using the DID resolver instead of the SDK. The DID resolver may offer better performance for intensive version traversal operations since SDK queries can be rate-limited by RPC/REST API requests.

The DID resolver provides **dereferencing capabilities** that allow direct resolution of DID URLs pointing to specific resources. This leverages the [DID URL dereferencing specification](https://w3c.github.io/did-core/#did-url-dereferencing) to resolve resource references efficiently. For more details on DID resolution and dereferencing, see the [DID Resolver setup guide](../../did-resolver/setup-did-resolver.md).

```typescript
// Using cheqd resolver REST API for version navigation
async function getAllResourceMetadataViaResolver(collectionId: string) {
  // Resolve DID and get all resource metadata without pagination
  const resolverUrl = `https://resolver.cheqd.net/1.0/identifiers/${collectionId}`;
  const response = await fetch(resolverUrl);
  const didResolution = await response.json();
  
  // DLRs metadata is included in didDocumentMetadata.linkedResourceMetadata
  const resourceMetadata = didResolution.didDocumentMetadata?.linkedResourceMetadata || [];
  
  console.log(`Found ${resourceMetadata.length} resources for DID: ${collectionId}`);
  return resourceMetadata;
}

// Get latest version of a specific resource by name and type via resolver
async function getLatestResourceVersionViaResolver(collectionId: string, resourceName: string, resourceType: string) {
  // Resolve latest DLR version using resourceName and resourceType query parameters
  const resolverUrl = `${collectionId}?resourceName=${encodeURIComponent(resourceName)}&resourceType=${encodeURIComponent(resourceType)}`;
  const response = await fetch(`https://resolver.cheqd.net/1.0/identifiers/${resolverUrl}`);
  const resourceResolution = await response.json();
  
  if (!resourceResolution.contentStream) {
    throw new Error(`Latest version not found for resource: ${resourceName} (${resourceType})`);
  }
  
  console.log(`Resolved latest version of "${resourceName}" (${resourceType})`);
  return {
    content: resourceResolution.contentStream,
    metadata: resourceResolution.contentMetadata
  };
}

// Get specific resource version by ID via resolver
async function getSpecificResourceVersionViaResolver(collectionId: string, resourceId: string) {
  // Resolve specific DLR using resource path
  const resolverUrl = `https://resolver.cheqd.net/1.0/identifiers/${collectionId}/resources/${resourceId}`;
  const response = await fetch(resolverUrl);
  const resourceResolution = await response.json();
  
  if (!resourceResolution.contentStream) {
    throw new Error(`Resource not found: ${resourceId}`);
  }
  
  console.log(`Resolved specific resource: ${resourceId}`);
  return {
    content: resourceResolution.contentStream,
    metadata: resourceResolution.contentMetadata
  };
}

// Get specific resource metadata only (without content) via resolver
async function getSpecificResourceMetadataViaResolver(collectionId: string, resourceId: string) {
  // Resolve specific DLR metadata using metadata endpoint
  const resolverUrl = `https://resolver.cheqd.net/1.0/identifiers/${collectionId}/resources/${resourceId}/metadata`;
  const response = await fetch(resolverUrl);
  const metadataResolution = await response.json();
  
  if (!metadataResolution.contentMetadata) {
    throw new Error(`Resource metadata not found: ${resourceId}`);
  }
  
  console.log(`Resolved metadata for resource: ${resourceId}`);
  console.log(`Resource name: ${metadataResolution.contentMetadata.name}`);
  console.log(`Resource type: ${metadataResolution.contentMetadata.resourceType}`);
  console.log(`Media type: ${metadataResolution.contentMetadata.mediaType}`);
  console.log(`Version: ${metadataResolution.contentMetadata.resourceVersion || 'No version specified'}`);
  console.log(`Created: ${metadataResolution.contentMetadata.created}`);
  console.log(`Size: ${metadataResolution.contentMetadata.size || 'Unknown'} bytes`);
  
  return metadataResolution.contentMetadata;
}

// Navigate version chain using resolver metadata
async function getVersionChainViaResolver(collectionId: string, resourceName: string, resourceType: string) {
  // Step 1: Get all resource metadata for the DID
  const allResourceMetadata = await getResourceMetadataViaResolver(collectionId);
  
  // Step 2: Filter resources by name and type to find version chain
  const versionChainMetadata = allResourceMetadata.filter(resource => 
    resource.name === resourceName && resource.resourceType === resourceType
  );
  
  if (versionChainMetadata.length === 0) {
    throw new Error(`No resources found with name "${resourceName}" and type "${resourceType}"`);
  }
  
  // Step 3: Sort by creation time to establish chain order
  versionChainMetadata.sort((a, b) => new Date(a.created).getTime() - new Date(b.created).getTime());
  
  console.log(`Found version chain with ${versionChainMetadata.length} versions:`);
  versionChainMetadata.forEach((version, index) => {
    console.log(`  Version ${index + 1}: ${version.id} (${version.resourceVersion || 'no version'})`);
  });
  
  // Step 4: Optionally resolve content for each version
  const versionsWithContent = [];
  for (const metadata of versionChainMetadata) {
    try {
      const versionContent = await getSpecificResourceVersionViaResolver(collectionId, metadata.id);
      versionsWithContent.push({
        metadata,
        content: versionContent.content,
        contentMetadata: versionContent.metadata
      });
    } catch (error) {
      console.warn(`Could not resolve content for version ${metadata.id}:`, error);
      versionsWithContent.push({
        metadata,
        content: null,
        contentMetadata: null
      });
    }
  }
  
  return {
    resourceName,
    resourceType,
    totalVersions: versionChainMetadata.length,
    firstVersion: versionChainMetadata[0],
    latestVersion: versionChainMetadata[versionChainMetadata.length - 1],
    allVersions: versionsWithContent
  };
}

// Complete example: Find and resolve latest version
async function resolveLatestResourceVersion(collectionId: string, resourceName: string, resourceType: string) {
  try {
    console.log(`🔍 Resolving latest version of "${resourceName}" (${resourceType})`);
    
    // Method 1: Direct resolution of latest version
    const latestVersion = await getLatestResourceVersionViaResolver(collectionId, resourceName, resourceType);
    
    console.log(`✅ Latest version resolved:`);
    console.log(`📄 Content size: ${JSON.stringify(latestVersion.content).length} bytes`);
    console.log(`🏷️  Media type: ${latestVersion.metadata?.mediaType}`);
    console.log(`📅 Created: ${latestVersion.metadata?.created}`);
    
    return latestVersion;
    
  } catch (error) {
    console.error(`❌ Failed to resolve latest version:`, error);
    
    // Fallback: Use metadata-based resolution
    console.log(`🔄 Trying metadata-based resolution...`);
    const versionChain = await getVersionChainViaResolver(collectionId, resourceName, resourceType);
    
    if (versionChain.latestVersion) {
      console.log(`✅ Found via metadata: ${versionChain.latestVersion.id}`);
      return versionChain.allVersions[versionChain.allVersions.length - 1];
    }
    
    throw new Error(`Could not resolve resource: ${resourceName} (${resourceType})`);
  }
}
```

### Performance Considerations

- **SDK Queries**: Direct SDK queries may be subject to RPC/REST API rate limiting
- **DID Resolver**: May provide better performance for intensive version navigation
- **Batch Operations**: Consider resolver for operations requiring many sequential queries
- **Caching**: Implement caching strategies regardless of method chosen

## Best Practices

### Version Management Strategy

1. **Semantic Versioning**: Use semantic versioning (major.minor.patch) for clarity
2. **Meaningful Changes**: Only create versions for meaningful changes
3. **Version Documentation**: Include change logs in your resource content
4. **Chain Navigation**: Implement proper chain traversal for your applications
5. **Latest Version Logic**: Always check for the latest version in your applications

### Content Management

1. **Immutability Awareness**: Remember that all versions are permanent and immutable
2. **Storage Efficiency**: Be mindful of storage costs when creating frequent versions
3. **Content Validation**: Validate content before creating versions
4. **Metadata Consistency**: Keep metadata consistent across versions
5. **Breaking Changes**: Use major version increments for breaking changes
6. **Fixed Pricing by Content Type**: Resource creation costs vary based on content type (JSON, image, default). Use the appropriate fee generator functions (`generateCreateResourceJsonFees`, `generateCreateResourceImageFees`, `generateCreateResourceDefaultFees`) or delegate fee calculation to the broadcasting functions, which will automatically set fees based on the resource's media type.

### Query Performance Considerations

1. **Chain Length**: Long version chains may impact query performance
2. **Batch Operations**: Group related version creations when possible
3. **Caching Strategy**: Cache frequently accessed versions
4. **Index Management**: Maintain indexes of latest versions for quick access
5. **Pruning Strategy**: Plan for archival of very old versions if needed

## Error Handling and Validation

### Version Chain Validation

Version chains are automatically maintained by the cheqd ledger and should always be consistent. Here's an example showing what a healthy version chain looks like:

```typescript
// Example: Checking version chain consistency
async function demonstrateVersionChainConsistency(sdk: any, collectionId: string, resourceId: string) {
  const resource = await sdk.queryResource(collectionId, resourceId);
  
  console.log('📋 Resource Chain Info:');
  console.log(`ID: ${resource.header.id}`);
  console.log(`Name: ${resource.header.name}`);
  console.log(`Type: ${resource.header.resourceType}`);
  console.log(`Version: ${resource.header.version}`);
  console.log(`Previous: ${resource.header.previousVersionId || 'None (first version)'}`);
  console.log(`Next: ${resource.header.nextVersionId || 'None (latest version)'}`);
  
  // The ledger automatically ensures:
  // 1. All versions with same name + type are linked
  // 2. Previous/next IDs always point to each other correctly
  // 3. Only the DID controller can create versions
  // 4. Version chains cannot be broken or corrupted
  
  return resource;
}

// Simple validation check (minimal querying)
async function quickVersionCheck(sdk: any, collectionId: string, resourceId: string) {
  try {
    const resource = await sdk.queryResource(collectionId, resourceId);
    console.log(`✅ Resource exists: ${resource.header.name} v${resource.header.version}`);
    
    // Check if it's part of a version chain
    const hasVersions = resource.header.previousVersionId || resource.header.nextVersionId;
    if (hasVersions) {
      console.log(`🔗 Part of version chain (${resource.header.previousVersionId ? 'has previous' : 'first'}, ${resource.header.nextVersionId ? 'has next' : 'latest'})`);
    } else {
      console.log(`🆕 Standalone resource (no versions)`);
    }
    
    return true;
  } catch (error) {
    console.error(`❌ Resource validation failed:`, error);
    return false;
  }
}
```

### Resource Creation Validation

```typescript
function validateVersionCreation(existingResource: any, newResourcePayload: any) {
  // Check if this will create a version vs new resource
  const sameNameAndType = 
    existingResource.header.name === newResourcePayload.header.name &&
    existingResource.header.resourceType === newResourcePayload.header.resourceType;
  
  if (sameNameAndType) {
    // This will create a version
    // Note: previousVersionId and nextVersionId are automatically set by the ledger during creation
    console.log(`✅ Will create new version of "${existingResource.header.name}"`);
    console.log(`🔗 Version linking will be handled automatically by the ledger`);
  } else {
    // This will create a new resource
    console.log(`✅ Will create new resource (name or type differs)`);
    console.log(`🆕 No version linking - starts a new resource chain`);
  }
  
  return sameNameAndType;
}
```

## Troubleshooting

### Common Issues

1. **Broken Version Chains**: Check that previousVersionId and nextVersionId are properly linked
2. **Accidental New Resources**: Verify name and resourceType match exactly for versioning
3. **Missing Previous Version**: Ensure previousVersionId points to an existing resource
4. **Permission Denied**: Verify signing keys have authority over the collection DID
5. **Fee Calculation Errors**: Use appropriate fee methods for your resource media type

### Version Chain Recovery

```typescript
async function repairVersionChain(sdk: any, collectionId: string) {
  // This is a theoretical recovery function - version chains should not break
  // in normal operation, but this shows how you might analyze issues
  
  const allResources = await sdk.queryCollectionResources(collectionId);
  
  // Group by name and resource type
  const resourceGroups = new Map();
  
  for (const resource of allResources) {
    const key = `${resource.header.name}::${resource.header.resourceType}`;
    if (!resourceGroups.has(key)) {
      resourceGroups.set(key, []);
    }
    resourceGroups.get(key).push(resource);
  }
  
  // Analyze each group for chain integrity
  for (const [key, versions] of resourceGroups) {
    console.log(`Analyzing version chain: ${key}`);
    console.log(`Total versions: ${versions.length}`);
    
    // Sort by creation time or version number
    versions.sort((a, b) => new Date(a.header.created).getTime() - new Date(b.header.created).getTime());
    
    // Check for chain continuity
    let chainIssues = [];
    
    versions.forEach((version, index) => {
      if (index === 0) {
        // First version should have no previousVersionId
        if (version.header.previousVersionId) {
          chainIssues.push(`First version has previousVersionId: ${version.header.id}`);
        }
      } else {
        // Other versions should link to previous
        const expectedPrevious = versions[index - 1].header.id;
        if (version.header.previousVersionId !== expectedPrevious) {
          chainIssues.push(`Version ${version.header.id} has incorrect previousVersionId`);
        }
      }
      
      if (index === versions.length - 1) {
        // Last version should have no nextVersionId
        if (version.header.nextVersionId) {
          chainIssues.push(`Last version has nextVersionId: ${version.header.id}`);
        }
      } else {
        // Other versions should link to next
        const expectedNext = versions[index + 1].header.id;
        if (version.header.nextVersionId !== expectedNext) {
          chainIssues.push(`Version ${version.header.id} has incorrect nextVersionId`);
        }
      }
    });
    
    if (chainIssues.length === 0) {
      console.log(`✅ Version chain is healthy`);
    } else {
      console.log(`❌ Version chain issues found:`);
      chainIssues.forEach(issue => console.log(`  - ${issue}`));
    }
  }
}
```

## Next Steps

After managing resources, you can:

- [Query resource versions and history](query-resource.md)
- [Resolve resources via DID URLs](resolve-resource.md)
- Implement automated version management workflows
- Build version-aware applications that always use latest resources

## Getting Help

If you encounter issues:

- Check the [cheqd SDK GitHub repository](https://github.com/cheqd/sdk) for updates
- Review the [test files](https://github.com/cheqd/sdk/tree/main/tests) for examples
- Consult the [cheqd documentation](https://docs.cheqd.io/) for comprehensive guides
- Join the [cheqd Community Discord](https://discord.gg/cheqd) for support
