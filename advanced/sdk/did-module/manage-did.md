# Update and Deactivate DIDs with cheqd SDK

This guide demonstrates how to update and deactivate Decentralized Identifiers (DIDs) using the cheqd SDK, with examples based on the SDK's test suite and common usage patterns.

## Prerequisites

Before managing DIDs, ensure you have:

1. [Installed and configured the cheqd SDK](../sdk-setup.md)
2. [Created a DID](create-did.md) that you want to update or deactivate
3. A funded wallet for paying transaction fees
4. Access to a cheqd network (testnet or mainnet)
5. The private keys associated with the DID's authentication methods

## SDK Initialization

First, initialize the SDK with the required modules:

### Basic SDK Setup

```typescript
import { createCheqdSDK, DIDModule, CheqdNetwork, AbstractCheqdSDKModule } from '@cheqd/sdk';
import { DirectSecp256k1HdWallet } from '@cosmjs/proto-signing';

// Initialize wallet from mnemonic
const wallet = await DirectSecp256k1HdWallet.fromMnemonic(
  'your twelve word mnemonic phrase goes here like this example', 
  { prefix: 'cheqd' }
);

// Create SDK instance
const sdk = await createCheqdSDK({
  modules: [DIDModule as unknown as AbstractCheqdSDKModule],
  rpcUrl: 'https://rpc.cheqd.network', // testnet
  network: CheqdNetwork.Testnet,
  wallet: wallet
});
```

### Advanced SDK Setup with Custom Configuration

```typescript
import { 
  createCheqdSDK, 
  DIDModule, 
  FeemarketModule,
  CheqdNetwork,
  createDefaultCheqdRegistry,
  CheqdSigningStargateClient,
  CheqdQuerier,
  setupDidExtension,
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
      FeemarketModule as unknown as AbstractCheqdSDKModule
    ],
    rpcUrl: 'https://rpc.cheqd.network',
    network: CheqdNetwork.Testnet,
    wallet: wallet
  };
  
  const sdk = await createCheqdSDK(options);
  
  // Initialize module instances for direct usage
  const registry = createDefaultCheqdRegistry(DIDModule.registryTypes);
  const signer = await CheqdSigningStargateClient.connectWithSigner(
    'https://rpc.cheqd.network', 
    wallet, 
    { registry }
  );
  const querier = await CheqdQuerier.connectWithExtension(
    'https://rpc.cheqd.network', 
    setupDidExtension
  );
  
  const didModule = new DIDModule(signer, querier);
  
  return { sdk, didModule, wallet };
}
```

## DID Update Examples

### Example 1: Basic DID Update - Add Verification Method

This example shows how to add a new verification method to an existing DID:

```typescript
import { 
  createKeyPairBase64,
  createVerificationKeys,
  createDidVerificationMethod,
  MethodSpecificIdAlgo,
  VerificationMethods,
  toString,
  fromString,
  type ISignInputs,
  type DidStdFee
} from '@cheqd/sdk';

async function addVerificationMethodToDID() {
  // Initialize SDK (see SDK setup above)
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // Existing DID information
  const existingDID = 'did:cheqd:testnet:existing-did-id';
  
  // Step 1: Query existing DID document
  const existingDidDoc = await didModule.queryDidDoc(existingDID);
  if (!existingDidDoc.didDoc) {
    throw new Error(`DID document not found: ${existingDID}`);
  }
  
  console.log(`Found existing DID: ${existingDID}`);
  
  // Step 2: Generate new key pair for additional verification method
  const newKeyPair = createKeyPairBase64();
  
  // Step 3: Create new verification keys
  const newVerificationKeys = createVerificationKeys(
    newKeyPair.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-2', // New key ID
    CheqdNetwork.Testnet,
    existingDidDoc.didDoc.id.split(':').pop()!, // Extract method-specific ID
    existingDidDoc.didDoc.id
  );
  
  // Step 4: Create new verification method
  const newVerificationMethods = createDidVerificationMethod(
    [VerificationMethods.Ed255192020],
    [newVerificationKeys]
  );
  
  // Step 5: Update the DID document by adding new verification method
  const updatedDidDoc = {
    ...existingDidDoc.didDoc,
    verificationMethod: [
      ...(existingDidDoc.didDoc.verificationMethod || []),
      ...newVerificationMethods
    ],
    authentication: [
      ...(existingDidDoc.didDoc.authentication || []),
      newVerificationMethods[0].id
    ]
  };
  
  // Step 6: Create sign inputs (sign with both existing and new keys)
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: existingDidDoc.didDoc.verificationMethod![0].id,
      privateKeyHex: 'your-existing-private-key-hex', // Replace with actual existing key
    },
    {
      verificationMethodId: newVerificationMethods[0].id,
      privateKeyHex: toString(fromString(newKeyPair.privateKey, 'base64'), 'hex'), // New key
    },
  ];
  
  // Step 7: Get fee payer and calculate fees
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await DIDModule.generateUpdateDidDocFees(feePayer);
  
  // Step 8: Update the DID
  // Note: This can also be called as sdk.updateDidDocTx(signInputs, updatedDidDoc, feePayer, fee)
  const updateTx = await didModule.updateDidDocTx(
    signInputs, 
    updatedDidDoc, 
    feePayer, 
    fee
  );
  
  if (updateTx.code === 0) {
    console.log('✅ DID updated successfully!');
    console.log(`DID: ${updatedDidDoc.id}`);
    console.log(`Transaction hash: ${updateTx.transactionHash}`);
    console.log(`Added verification method: ${newVerificationMethods[0].id}`);
    return {
      did: updatedDidDoc.id,
      didDocument: updatedDidDoc,
      transaction: updateTx,
      newKeyPair
    };
  } else {
    throw new Error(`Failed to update DID: ${updateTx.rawLog}`);
  }
}
```

### Example 2: Update DID Services

This example shows how to update service endpoints in a DID document:

```typescript
async function updateDIDServices() {
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // Existing DID information
  const existingDID = 'did:cheqd:testnet:existing-did-id';
  
  // Query existing DID document
  const existingDidDoc = await didModule.queryDidDoc(existingDID);
  if (!existingDidDoc.didDoc) {
    throw new Error(`DID document not found: ${existingDID}`);
  }
  
  // Update services
  const updatedDidDoc = {
    ...existingDidDoc.didDoc,
    service: [
      {
        id: `${existingDidDoc.didDoc.id}#service-1`,
        type: 'LinkedDomains',
        serviceEndpoint: 'https://updated-example.com'
      },
      {
        id: `${existingDidDoc.didDoc.id}#service-2`,
        type: 'DIDCommMessaging',
        serviceEndpoint: 'https://updated-example.com/didcomm',
        accept: ['didcomm/v2'],
        routingKeys: ['did:example:somemediator#somekey']
      },
      {
        id: `${existingDidDoc.didDoc.id}#service-3`,
        type: 'CredentialRegistry',
        serviceEndpoint: 'https://registry.example.com'
      }
    ]
  };
  
  // Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: existingDidDoc.didDoc.verificationMethod![0].id,
      privateKeyHex: 'your-existing-private-key-hex', // Replace with actual key
    },
  ];
  
  // Execute update
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await DIDModule.generateUpdateDidDocFees(feePayer);
  // Note: This can also be called as sdk.updateDidDocTx(signInputs, updatedDidDoc, feePayer, fee)
  const updateTx = await didModule.updateDidDocTx(signInputs, updatedDidDoc, feePayer, fee);
  
  console.log(`Services updated for DID: ${updatedDidDoc.id}`);
  console.log(`Number of services: ${updatedDidDoc.service?.length}`);
  
  return { updatedDidDoc, updateTx };
}
```

### Example 3: Rotate Verification Keys

This example shows how to rotate (replace) existing verification keys:

```typescript
async function rotateVerificationKeys() {
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // Existing DID information
  const existingDID = 'did:cheqd:testnet:existing-did-id';
  
  // Query existing DID document
  const existingDidDoc = await didModule.queryDidDoc(existingDID);
  if (!existingDidDoc.didDoc) {
    throw new Error(`DID document not found: ${existingDID}`);
  }
  
  // Generate new key pair for rotation
  const newKeyPair = createKeyPairBase64();
  
  // Create new verification keys (same key ID, new key material)
  const rotatedVerificationKeys = createVerificationKeys(
    newKeyPair.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-1', // Same key ID as existing
    CheqdNetwork.Testnet,
    existingDidDoc.didDoc.id.split(':').pop()!,
    existingDidDoc.didDoc.id
  );
  
  // Create new verification method
  const rotatedVerificationMethods = createDidVerificationMethod(
    [VerificationMethods.Ed255192020],
    [rotatedVerificationKeys]
  );
  
  // Update DID document with rotated key
  const updatedDidDoc = {
    ...existingDidDoc.didDoc,
    verificationMethod: rotatedVerificationMethods,
    authentication: [rotatedVerificationMethods[0].id],
    assertionMethod: [rotatedVerificationMethods[0].id],
    capabilityInvocation: [rotatedVerificationMethods[0].id],
    capabilityDelegation: [rotatedVerificationMethods[0].id]
  };
  
  // IMPORTANT: Sign with both OLD keys (to authorize) and NEW keys (to establish trust)
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: existingDidDoc.didDoc.verificationMethod![0].id,
      privateKeyHex: 'your-old-private-key-hex', // OLD key to authorize the rotation
    },
    {
      verificationMethodId: rotatedVerificationMethods[0].id,
      privateKeyHex: toString(fromString(newKeyPair.privateKey, 'base64'), 'hex'), // NEW key
    },
  ];
  
  // Execute key rotation
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await DIDModule.generateUpdateDidDocFees(feePayer);
  // Note: This can also be called as sdk.updateDidDocTx(signInputs, updatedDidDoc, feePayer, fee)
  const rotationTx = await didModule.updateDidDocTx(signInputs, updatedDidDoc, feePayer, fee);
  
  if (rotationTx.code === 0) {
    console.log('✅ Keys rotated successfully!');
    console.log(`DID: ${updatedDidDoc.id}`);
    console.log(`New verification method: ${rotatedVerificationMethods[0].id}`);
    return {
      did: updatedDidDoc.id,
      didDocument: updatedDidDoc,
      transaction: rotationTx,
      newKeyPair,
      oldKeyDeactivated: true
    };
  } else {
    throw new Error(`Failed to rotate keys: ${rotationTx.rawLog}`);
  }
}
```

### Example 4: Update DID with Custom Version ID

This example shows how to update a DID with a custom version identifier:

```typescript
import { v4 as uuidv4 } from 'uuid';

async function updateDIDWithVersionId() {
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // Existing DID information
  const existingDID = 'did:cheqd:testnet:existing-did-id';
  
  // Query existing DID document
  const existingDidDoc = await didModule.queryDidDoc(existingDID);
  if (!existingDidDoc.didDoc) {
    throw new Error(`DID document not found: ${existingDID}`);
  }
  
  // Make a simple update (add context)
  const updatedDidDoc = {
    ...existingDidDoc.didDoc,
    '@context': [
      'https://www.w3.org/ns/did/v1',
      'https://w3id.org/security/suites/ed25519-2020/v1',
      'https://custom-context.example.com/v1'
    ]
  };
  
  // Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: existingDidDoc.didDoc.verificationMethod![0].id,
      privateKeyHex: 'your-existing-private-key-hex',
    },
  ];
  
  // Generate custom version ID
  const versionId = uuidv4();
  
  // Execute update with version ID
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await DIDModule.generateUpdateDidDocFees(feePayer);
  // Note: This can also be called as sdk.updateDidDocTx(signInputs, updatedDidDoc, feePayer, fee, undefined, versionId)
  const updateTx = await didModule.updateDidDocTx(
    signInputs, 
    updatedDidDoc, 
    feePayer, 
    fee,
    undefined,
    versionId
  );
  
  console.log(`DID updated with version ID: ${versionId}`);
  console.log(`DID: ${updatedDidDoc.id}`);
  
  return { updatedDidDoc, updateTx, versionId };
}
```

## DID Deactivation Examples

### Example 1: Basic DID Deactivation

This example shows how to deactivate a DID:

```typescript
async function deactivateDID() {
  // Initialize SDK (see SDK setup above)
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // DID to deactivate
  const didToDeactivate = 'did:cheqd:testnet:did-to-deactivate';
  
  // Step 1: Query existing DID document to verify it exists
  const existingDidDoc = await didModule.queryDidDoc(didToDeactivate);
  if (!existingDidDoc.didDoc) {
    throw new Error(`DID document not found: ${didToDeactivate}`);
  }
  
  console.log(`Found DID to deactivate: ${didToDeactivate}`);
  
  // Step 2: Create sign inputs (must use existing authentication keys)
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: existingDidDoc.didDoc.verificationMethod![0].id,
      privateKeyHex: 'your-existing-private-key-hex', // Must be current DID controller key
    },
  ];
  
  // Step 3: Get fee payer and calculate fees
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await DIDModule.generateDeactivateDidDocFees(feePayer);
  
  // Step 4: Deactivate the DID
  // Note: This can also be called as sdk.deactivateDidDocTx(signInputs, didToDeactivate, feePayer, fee)
  const deactivateTx = await didModule.deactivateDidDocTx(
    signInputs, 
    didToDeactivate, 
    feePayer, 
    fee
  );
  
  if (deactivateTx.code === 0) {
    console.log('✅ DID deactivated successfully!');
    console.log(`DID: ${didToDeactivate}`);
    console.log(`Transaction hash: ${deactivateTx.transactionHash}`);
    
    // Verify deactivation by querying
    const deactivatedDoc = await didModule.queryDidDoc(didToDeactivate);
    console.log(`DID is now deactivated: ${deactivatedDoc.didDocMetadata?.deactivated}`);
    
    return {
      did: didToDeactivate,
      transaction: deactivateTx,
      deactivated: true
    };
  } else {
    throw new Error(`Failed to deactivate DID: ${deactivateTx.rawLog}`);
  }
}
```

### Example 2: Deactivate DID with Custom Version ID

This example shows how to deactivate a DID with a custom version identifier:

```typescript
async function deactivateDIDWithVersionId() {
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // DID to deactivate
  const didToDeactivate = 'did:cheqd:testnet:did-to-deactivate';
  
  // Query existing DID document
  const existingDidDoc = await didModule.queryDidDoc(didToDeactivate);
  if (!existingDidDoc.didDoc) {
    throw new Error(`DID document not found: ${didToDeactivate}`);
  }
  
  // Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: existingDidDoc.didDoc.verificationMethod![0].id,
      privateKeyHex: 'your-existing-private-key-hex',
    },
  ];
  
  // Generate custom version ID for deactivation
  const versionId = uuidv4();
  
  // Execute deactivation with version ID
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await DIDModule.generateDeactivateDidDocFees(feePayer);
  // Note: This can also be called as sdk.deactivateDidDocTx(signInputs, didToDeactivate, feePayer, fee, undefined, versionId)
  const deactivateTx = await didModule.deactivateDidDocTx(
    signInputs, 
    didToDeactivate, 
    feePayer, 
    fee,
    undefined,
    versionId
  );
  
  console.log(`DID deactivated with version ID: ${versionId}`);
  console.log(`DID: ${didToDeactivate}`);
  
  return { did: didToDeactivate, deactivateTx, versionId };
}
```

### Example 3: Bulk DID Deactivation

This example shows how to deactivate multiple DIDs in sequence:

```typescript
async function bulkDeactivateDIDs() {
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // DIDs to deactivate
  const didsToDeactivate = [
    'did:cheqd:testnet:did-1',
    'did:cheqd:testnet:did-2', 
    'did:cheqd:testnet:did-3'
  ];
  
  const deactivationResults = [];
  
  for (const didId of didsToDeactivate) {
    try {
      // Query existing DID document
      const existingDidDoc = await didModule.queryDidDoc(didId);
      if (!existingDidDoc.didDoc) {
        console.warn(`Skipping ${didId}: DID document not found`);
        continue;
      }
      
      // Create sign inputs (assuming you have the keys)
      const signInputs: ISignInputs[] = [
        {
          verificationMethodId: existingDidDoc.didDoc.verificationMethod![0].id,
          privateKeyHex: 'your-private-key-for-' + didId.split(':').pop(), // Adjust as needed
        },
      ];
      
      // Execute deactivation
      const feePayer = (await wallet.getAccounts())[0].address;
      const fee = await DIDModule.generateDeactivateDidDocFees(feePayer);
      // Note: This can also be called as sdk.deactivateDidDocTx(signInputs, didId, feePayer, fee)
      const deactivateTx = await didModule.deactivateDidDocTx(signInputs, didId, feePayer, fee);
      
      deactivationResults.push({
        did: didId,
        success: deactivateTx.code === 0,
        transactionHash: deactivateTx.transactionHash,
        error: deactivateTx.code !== 0 ? deactivateTx.rawLog : null
      });
      
      console.log(`${deactivateTx.code === 0 ? '✅' : '❌'} ${didId}: ${deactivateTx.code === 0 ? 'Deactivated' : 'Failed'}`);
      
      // Add delay between transactions to avoid rate limiting
      await new Promise(resolve => setTimeout(resolve, 1000));
      
    } catch (error) {
      console.error(`Error deactivating ${didId}:`, error);
      deactivationResults.push({
        did: didId,
        success: false,
        error: error instanceof Error ? error.message : 'Unknown error'
      });
    }
  }
  
  const successCount = deactivationResults.filter(r => r.success).length;
  console.log(`\n📊 Bulk deactivation complete: ${successCount}/${didsToDeactivate.length} DIDs deactivated`);
  
  return deactivationResults;
}
```

## Complete Working Examples

### Complete Update Example

Here's a complete example for updating a DID:

```typescript
import { 
  createCheqdSDK, 
  DIDModule,
  CheqdNetwork,
  AbstractCheqdSDKModule,
  createKeyPairBase64,
  createVerificationKeys,
  createDidVerificationMethod,
  MethodSpecificIdAlgo,
  VerificationMethods,
  toString,
  fromString,
  type ISignInputs
} from '@cheqd/sdk';
import { DirectSecp256k1HdWallet } from '@cosmjs/proto-signing';

async function completeUpdateExample() {
  try {
    // Replace with your mnemonic
    const mnemonic = 'abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon about';
    
    // Initialize wallet
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { 
      prefix: 'cheqd' 
    });
    
    // Create SDK instance
    const sdk = await createCheqdSDK({
      modules: [DIDModule as unknown as AbstractCheqdSDKModule],
      rpcUrl: 'https://rpc.cheqd.network', // testnet
      network: CheqdNetwork.Testnet,
      wallet: wallet
    });
    
    // DID to update
    const didToUpdate = 'did:cheqd:testnet:your-existing-did';
    
    // Query existing DID document
    const existingDidDoc = await sdk.queryDidDoc(didToUpdate);
    if (!existingDidDoc.didDoc) {
      throw new Error(`DID document not found: ${didToUpdate}`);
    }
    
    console.log(`Found existing DID: ${didToUpdate}`);
    
    // Add new service endpoint
    const updatedDidDoc = {
      ...existingDidDoc.didDoc,
      service: [
        ...(existingDidDoc.didDoc.service || []),
        {
          id: `${existingDidDoc.didDoc.id}#new-service`,
          type: 'LinkedDomains',
          serviceEndpoint: 'https://new-service.example.com'
        }
      ]
    };
    
    // Create sign inputs
    const signInputs: ISignInputs[] = [
      {
        verificationMethodId: existingDidDoc.didDoc.verificationMethod![0].id,
        privateKeyHex: 'your-existing-private-key-hex', // Replace with your actual key
      },
    ];
    
    // Get fee payer
    const feePayer = (await wallet.getAccounts())[0].address;
    console.log(`Fee payer: ${feePayer}`);
    
    // Calculate fees
    const fee = await DIDModule.generateUpdateDidDocFees(feePayer);
    console.log(`Calculated fee: ${JSON.stringify(fee)}`);
    
    // Update DID using SDK dot notation
    const result = await sdk.updateDidDocTx(signInputs, updatedDidDoc, feePayer, fee);
    
    if (result.code === 0) {
      console.log('✅ DID updated successfully!');
      console.log(`🆔 DID: ${updatedDidDoc.id}`);
      console.log(`📄 Transaction hash: ${result.transactionHash}`);
      console.log(`⛽ Gas used: ${result.gasUsed}`);
      console.log(`🔗 Services: ${updatedDidDoc.service?.length}`);
    } else {
      console.error(`❌ Failed to update DID: ${result.rawLog}`);
    }
    
  } catch (error) {
    console.error('Error updating DID:', error);
  }
}

// Run the example
completeUpdateExample();
```

### Complete Deactivation Example

Here's a complete example for deactivating a DID:

```typescript
async function completeDeactivationExample() {
  try {
    // Replace with your mnemonic
    const mnemonic = 'abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon about';
    
    // Initialize wallet
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { 
      prefix: 'cheqd' 
    });
    
    // Create SDK instance
    const sdk = await createCheqdSDK({
      modules: [DIDModule as unknown as AbstractCheqdSDKModule],
      rpcUrl: 'https://rpc.cheqd.network', // testnet
      network: CheqdNetwork.Testnet,
      wallet: wallet
    });
    
    // DID to deactivate
    const didToDeactivate = 'did:cheqd:testnet:your-existing-did';
    
    // Query existing DID document to verify it exists and is active
    const existingDidDoc = await sdk.queryDidDoc(didToDeactivate);
    if (!existingDidDoc.didDoc) {
      throw new Error(`DID document not found: ${didToDeactivate}`);
    }
    
    if (existingDidDoc.didDocMetadata?.deactivated) {
      throw new Error(`DID is already deactivated: ${didToDeactivate}`);
    }
    
    console.log(`Found active DID: ${didToDeactivate}`);
    
    // Create sign inputs
    const signInputs: ISignInputs[] = [
      {
        verificationMethodId: existingDidDoc.didDoc.verificationMethod![0].id,
        privateKeyHex: 'your-existing-private-key-hex', // Replace with your actual key
      },
    ];
    
    // Get fee payer
    const feePayer = (await wallet.getAccounts())[0].address;
    console.log(`Fee payer: ${feePayer}`);
    
    // Calculate fees
    const fee = await DIDModule.generateDeactivateDidDocFees(feePayer);
    console.log(`Calculated fee: ${JSON.stringify(fee)}`);
    
    // Deactivate DID using SDK dot notation
    const result = await sdk.deactivateDidDocTx(signInputs, didToDeactivate, feePayer, fee);
    
    if (result.code === 0) {
      console.log('✅ DID deactivated successfully!');
      console.log(`🆔 DID: ${didToDeactivate}`);
      console.log(`📄 Transaction hash: ${result.transactionHash}`);
      console.log(`⛽ Gas used: ${result.gasUsed}`);
      
      // Verify deactivation
      const deactivatedDoc = await sdk.queryDidDoc(didToDeactivate);
      console.log(`🚫 DID is now deactivated: ${deactivatedDoc.didDocMetadata?.deactivated}`);
    } else {
      console.error(`❌ Failed to deactivate DID: ${result.rawLog}`);
    }
    
  } catch (error) {
    console.error('Error deactivating DID:', error);
  }
}

// Run the example
completeDeactivationExample();
```

## Error Handling and Validation

### Pre-update Validation

Before updating a DID, validate the changes:

```typescript
import { validateSpecCompliantPayload } from '@cheqd/sdk';

// Validate updated DID payload before submission
const validation = validateSpecCompliantPayload(updatedDidDoc);
if (!validation.valid) {
  throw new Error(`Invalid DID document: ${validation.error}`);
}
console.log('✅ DID document validation passed');
```

### Authentication Validation

Validate that your sign inputs have permission to update the DID:

```typescript
const authValidation = await DIDModule.validateAuthenticationAgainstSignatures(
  existingDidDoc.didDoc,
  signInputs.map(input => ({
    verificationMethodId: input.verificationMethodId,
    signature: new Uint8Array() // Empty for validation
  }))
);

if (!authValidation.valid) {
  throw new Error(`Authentication validation failed: ${authValidation.error}`);
}
console.log('✅ Authentication validation passed');
```

### DID Status Check

Before deactivating, check if DID is already deactivated:

```typescript
async function checkDIDStatus(didId: string, didModule: DIDModule) {
  const didDoc = await didModule.queryDidDoc(didId);
  
  if (!didDoc.didDoc) {
    return { exists: false, active: false };
  }
  
  const isActive = !didDoc.didDocMetadata?.deactivated;
  
  return {
    exists: true,
    active: isActive,
    metadata: didDoc.didDocMetadata
  };
}
```

## Best Practices

### For DID Updates

1. **Always Query First**: Query the existing DID document before making updates
2. **Preserve Critical Fields**: Maintain essential fields like `id` and `@context`
3. **Key Rotation Security**: When rotating keys, always sign with the current keys
4. **Incremental Updates**: Make small, incremental changes rather than large restructures
5. **Version Control**: Consider using version IDs for important updates

### For DID Deactivation

1. **Irreversible Action**: Remember that DID deactivation is irreversible
2. **Clean Up Resources**: Deactivate or update any DID-Linked Resources before deactivating the DID
3. **Notify Dependents**: Inform any systems or users that depend on the DID
4. **Backup Keys**: Ensure you have secure backups of private keys before deactivation
5. **Verify Authority**: Double-check that you have the authority to deactivate the DID

### General Best Practices

1. **Key Management**: Store private keys securely and never expose them
2. **Fee Management**: Monitor transaction fees and maintain adequate wallet balances
3. **Network Selection**: Use testnet for development and testing
4. **Error Handling**: Implement comprehensive error handling
5. **Transaction Monitoring**: Monitor transaction status and handle failures gracefully

## Next Steps

After managing DIDs, you can:

- [Create DID-Linked Resources](../resource-module/create-resource.md)
- [Manage DID-Linked Resources](../resource-module/manage-resource.md)
- Set up automated DID management workflows

## Troubleshooting

### Common Issues

1. **Permission Denied**: Ensure you're signing with keys that have update/deactivate authority
2. **DID Not Found**: Verify the DID exists and the network is correct
3. **Already Deactivated**: Check DID status before attempting deactivation
4. **Insufficient Funds**: Ensure wallet has enough CHEQ tokens for fees
5. **Invalid Signatures**: Verify private keys match the DID's verification methods

### Getting Help

If you encounter issues:

- Check the [cheqd SDK GitHub repository](https://github.com/cheqd/sdk) for updates
- Review the [test files](https://github.com/cheqd/sdk/tree/main/esm/tests) for examples
- Consult the [cheqd documentation](https://docs.cheqd.io/) for comprehensive guides
- Join the [cheqd Community Discord](https://discord.gg/cheqd) for support
