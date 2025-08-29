# Create DIDs with cheqd SDK

This guide demonstrates how to create Decentralized Identifiers (DIDs) using the cheqd SDK, with examples based on the SDK's test suite and common usage patterns.

## Prerequisites

Before creating DIDs, ensure you have:

1. [Installed and configured the cheqd SDK](../sdk-setup.md)
2. A funded wallet for paying transaction fees
3. Access to a cheqd network (testnet or mainnet)

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

## DID Creation Examples

### Example 1: Basic DID Creation with Ed25519VerificationKey2020

This example creates a DID with a single Ed25519 key using the 2020 verification method:

```typescript
import { 
  createKeyPairBase64,
  createVerificationKeys,
  createDidVerificationMethod,
  createDidPayload,
  MethodSpecificIdAlgo,
  VerificationMethods,
  CheqdNetwork,
  toString,
  fromString,
  type ISignInputs
} from '@cheqd/sdk';

async function createBasicDID() {
  // Initialize SDK (see SDK setup above)
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // Step 1: Generate key pair
  const keyPair = createKeyPairBase64();
  
  // Step 2: Create verification keys
  const verificationKeys = createVerificationKeys(
    keyPair.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-1',
    CheqdNetwork.Testnet
  );
  
  // Step 3: Create verification methods
  const verificationMethods = createDidVerificationMethod(
    [VerificationMethods.Ed255192020],
    [verificationKeys]
  );
  
  // Step 4: Create DID payload
  const didPayload = createDidPayload(verificationMethods, [verificationKeys]);
  
  // Step 5: Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: didPayload.verificationMethod![0].id,
      privateKeyHex: toString(fromString(keyPair.privateKey, 'base64'), 'hex'),
    },
  ];
  
  // Step 6: Get fee payer address
  const feePayer = (await wallet.getAccounts())[0].address;
  
  // Step 7: Calculate transaction fees
  const fee = await DIDModule.generateCreateDidDocFees(feePayer);
  
  // Step 8: Create the DID
  // Note: This can also be called as sdk.createDidDocTx(signInputs, didPayload, feePayer, fee)
  const didTx = await didModule.createDidDocTx(
    signInputs, 
    didPayload, 
    feePayer, 
    fee
  );
  
  if (didTx.code === 0) {
    console.log('✅ DID created successfully!');
    console.log(`DID: ${didPayload.id}`);
    console.log(`Transaction hash: ${didTx.transactionHash}`);
    return {
      did: didPayload.id,
      didDocument: didPayload,
      transaction: didTx,
      keyPair
    };
  } else {
    throw new Error(`Failed to create DID: ${didTx.rawLog}`);
  }
}
```

### Example 2: DID Creation with JsonWebKey2020

This example creates a DID using the JsonWebKey2020 verification method:

```typescript
async function createDIDWithJWK() {
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // Generate key pair
  const keyPair = createKeyPairBase64();
  
  // Create verification keys with Base58 algorithm
  const verificationKeys = createVerificationKeys(
    keyPair.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-1',
    CheqdNetwork.Testnet
  );
  
  // Create verification methods with JsonWebKey2020
  const verificationMethods = createDidVerificationMethod(
    [VerificationMethods.JWK],
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
  
  // Execute transaction
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await DIDModule.generateCreateDidDocFees(feePayer);
  // Note: This can also be called as sdk.createDidDocTx(signInputs, didPayload, feePayer, fee)
  const didTx = await didModule.createDidDocTx(signInputs, didPayload, feePayer, fee);
  
  console.log(`JWK DID created: ${didPayload.id}`);
  return { didPayload, didTx, keyPair };
}
```

### Example 3: DID Creation with UUID Method Specific Identifier

This example creates a DID using UUID as the method-specific identifier instead of Base58:

```typescript
async function createDIDWithUUID() {
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // Generate key pair
  const keyPair = createKeyPairBase64();
  
  // Create verification keys with UUID algorithm
  const verificationKeys = createVerificationKeys(
    keyPair.publicKey,
    MethodSpecificIdAlgo.Uuid, // Using UUID instead of Base58
    'key-1',
    CheqdNetwork.Testnet
  );
  
  // Create verification methods
  const verificationMethods = createDidVerificationMethod(
    [VerificationMethods.Ed255192018],
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
  
  // Execute transaction
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await DIDModule.generateCreateDidDocFees(feePayer);
  // Note: This can also be called as sdk.createDidDocTx(signInputs, didPayload, feePayer, fee)
  const didTx = await didModule.createDidDocTx(signInputs, didPayload, feePayer, fee);
  
  console.log(`UUID-based DID created: ${didPayload.id}`);
  return { didPayload, didTx, keyPair };
}
```

### Example 4: Multi-Key DID Creation

This example creates a DID with multiple verification methods:

```typescript
async function createMultiKeyDID() {
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // Generate multiple key pairs
  const keyPair1 = createKeyPairBase64();
  const keyPair2 = createKeyPairBase64();
  
  // Create verification keys for both key pairs
  const verificationKeys1 = createVerificationKeys(
    keyPair1.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-1',
    CheqdNetwork.Testnet
  );
  
  const verificationKeys2 = createVerificationKeys(
    keyPair2.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-2',
    CheqdNetwork.Testnet,
    verificationKeys1.methodSpecificId, // Share same DID
    verificationKeys1.didUrl
  );
  
  // Create verification methods for both keys
  const verificationMethods = createDidVerificationMethod(
    [VerificationMethods.Ed255192020, VerificationMethods.JWK],
    [verificationKeys1, verificationKeys2]
  );
  
  // Create DID payload with both keys
  const didPayload = createDidPayload(
    verificationMethods, 
    [verificationKeys1, verificationKeys2]
  );
  
  // Create sign inputs for both keys
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: didPayload.verificationMethod![0].id,
      privateKeyHex: toString(fromString(keyPair1.privateKey, 'base64'), 'hex'),
    },
    {
      verificationMethodId: didPayload.verificationMethod![1].id,
      privateKeyHex: toString(fromString(keyPair2.privateKey, 'base64'), 'hex'),
    },
  ];
  
  // Execute transaction
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await DIDModule.generateCreateDidDocFees(feePayer);
  // Note: This can also be called as sdk.createDidDocTx(signInputs, didPayload, feePayer, fee)
  const didTx = await didModule.createDidDocTx(signInputs, didPayload, feePayer, fee);
  
  console.log(`Multi-key DID created: ${didPayload.id}`);
  console.log(`Number of verification methods: ${didPayload.verificationMethod?.length}`);
  
  return { 
    didPayload, 
    didTx, 
    keyPairs: [keyPair1, keyPair2],
    verificationKeys: [verificationKeys1, verificationKeys2]
  };
}
```

### Example 5: DID Creation with Services

This example creates a DID with service endpoints:

```typescript
async function createDIDWithServices() {
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // Generate key pair
  const keyPair = createKeyPairBase64();
  
  // Create verification keys
  const verificationKeys = createVerificationKeys(
    keyPair.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-1',
    CheqdNetwork.Testnet
  );
  
  // Create verification methods
  const verificationMethods = createDidVerificationMethod(
    [VerificationMethods.Ed255192020],
    [verificationKeys]
  );
  
  // Create DID payload
  const didPayload = createDidPayload(verificationMethods, [verificationKeys]);
  
  // Add service endpoints
  didPayload.service = [
    {
      id: `${didPayload.id}#service-1`,
      type: 'LinkedDomains',
      serviceEndpoint: 'https://example.com'
    },
    {
      id: `${didPayload.id}#service-2`,
      type: 'DIDCommMessaging',
      serviceEndpoint: 'https://example.com/didcomm',
      accept: ['didcomm/v2'],
      routingKeys: []
    }
  ];
  
  // Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: didPayload.verificationMethod![0].id,
      privateKeyHex: toString(fromString(keyPair.privateKey, 'base64'), 'hex'),
    },
  ];
  
  // Execute transaction
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await DIDModule.generateCreateDidDocFees(feePayer);
  // Note: This can also be called as sdk.createDidDocTx(signInputs, didPayload, feePayer, fee)
  const didTx = await didModule.createDidDocTx(signInputs, didPayload, feePayer, fee);
  
  console.log(`DID with services created: ${didPayload.id}`);
  console.log(`Number of services: ${didPayload.service?.length}`);
  
  return { didPayload, didTx, keyPair };
}
```

### Example 6: DID Creation with Custom Version ID

This example creates a DID with a custom version identifier:

```typescript
import { v4 as uuidv4 } from 'uuid';

async function createDIDWithVersionId() {
  const { didModule, wallet } = await initializeAdvancedSDK();
  
  // Generate key pair
  const keyPair = createKeyPairBase64();
  
  // Create verification keys
  const verificationKeys = createVerificationKeys(
    keyPair.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-1',
    CheqdNetwork.Testnet
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
  
  // Generate custom version ID
  const versionId = uuidv4();
  
  // Execute transaction with version ID
  const feePayer = (await wallet.getAccounts())[0].address;
  const fee = await DIDModule.generateCreateDidDocFees(feePayer);
  // Note: This can also be called as sdk.createDidDocTx(signInputs, didPayload, feePayer, fee, undefined, versionId)
  const didTx = await didModule.createDidDocTx(
    signInputs, 
    didPayload, 
    feePayer, 
    fee,
    undefined,
    versionId
  );
  
  console.log(`DID created with version ID: ${versionId}`);
  console.log(`DID: ${didPayload.id}`);
  
  return { didPayload, didTx, keyPair, versionId };
}
```

## Complete Working Example

Here's a complete example you can run:

```typescript
import { 
  createCheqdSDK, 
  DIDModule,
  CheqdNetwork,
  AbstractCheqdSDKModule,
  createKeyPairBase64,
  createVerificationKeys,
  createDidVerificationMethod,
  createDidPayload,
  MethodSpecificIdAlgo,
  VerificationMethods,
  toString,
  fromString,
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
      modules: [DIDModule as unknown as AbstractCheqdSDKModule],
      rpcUrl: 'https://rpc.cheqd.network', // testnet
      network: CheqdNetwork.Testnet,
      wallet: wallet
    });
    
    // Generate key pair
    const keyPair = createKeyPairBase64();
    console.log(`Generated key pair`);
    
    // Create verification keys
    const verificationKeys = createVerificationKeys(
      keyPair.publicKey,
      MethodSpecificIdAlgo.Base58,
      'key-1',
      CheqdNetwork.Testnet
    );
    console.log(`Created verification keys for DID: ${verificationKeys.didUrl}`);
    
    // Create verification methods
    const verificationMethods = createDidVerificationMethod(
      [VerificationMethods.Ed255192020],
      [verificationKeys]
    );
    
    // Create DID payload
    const didPayload = createDidPayload(verificationMethods, [verificationKeys]);
    console.log(`Created DID payload`);
    
    // Create sign inputs
    const signInputs: ISignInputs[] = [
      {
        verificationMethodId: didPayload.verificationMethod![0].id,
        privateKeyHex: toString(fromString(keyPair.privateKey, 'base64'), 'hex'),
      },
    ];
    
    // Get fee payer
    const feePayer = (await wallet.getAccounts())[0].address;
    console.log(`Fee payer: ${feePayer}`);
    
    // Calculate fees
    const fee = await DIDModule.generateCreateDidDocFees(feePayer);
    console.log(`Calculated fee: ${JSON.stringify(fee)}`);
    
    // Create DID using SDK dot notation
    const result = await sdk.createDidDocTx(signInputs, didPayload, feePayer, fee);
    
    if (result.code === 0) {
      console.log('✅ DID created successfully!');
      console.log(`🆔 DID: ${didPayload.id}`);
      console.log(`📄 Transaction hash: ${result.transactionHash}`);
      console.log(`⛽ Gas used: ${result.gasUsed}`);
    } else {
      console.error(`❌ Failed to create DID: ${result.rawLog}`);
    }
    
  } catch (error) {
    console.error('Error creating DID:', error);
  }
}

// Run the example
main();
```

## Error Handling and Validation

### Client-side Validation

Before submitting to the ledger, you can validate your DID payload:

```typescript
import { validateSpecCompliantPayload } from '@cheqd/sdk';

// Validate DID payload before submission
const validation = validateSpecCompliantPayload(didPayload);
console.log('Validation result:', validation);
```

### Authentication Validation

Validate that your sign inputs match the DID document authentication requirements:

```typescript
const authValidation = await DIDModule.validateAuthenticationAgainstSignatures(
  didPayload,
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

## Best Practices

1. **Key Management**: Store private keys securely and never expose them in client-side code
2. **Fee Estimation**: Always check fee requirements before submitting transactions
3. **Network Selection**: Use testnet for development and testing
4. **Error Handling**: Implement proper error handling for network and transaction failures
5. **Validation**: Validate DID documents and signatures before submitting to the ledger
6. **Gas Limits**: Be aware of gas limits when creating complex DID documents

## Next Steps

After creating a DID, you can:

- [Update the DID document](manage-did.md)
- [Create DID-Linked Resources](../resource-module/create-resource.md)
- [Deactivate the DID](manage-did.md#deactivating-a-did)
- Query the DID from the ledger

## Troubleshooting

### Common Issues

1. **Insufficient Funds**: Ensure your wallet has enough CHEQ tokens for transaction fees
2. **Network Connectivity**: Verify connection to the cheqd network RPC endpoint
3. **Key Format Errors**: Ensure keys are in the correct format (base64 or hex)
4. **Authentication Mismatches**: Verify that sign inputs match the DID's authentication methods

### Getting Help

If you encounter issues:

- Check the [cheqd SDK GitHub repository](https://github.com/cheqd/sdk) for the latest updates
- Review the [test files](https://github.com/cheqd/sdk/tree/main/esm/tests) for additional examples
- Consult the [cheqd documentation](https://docs.cheqd.io/) for comprehensive guides
- Join the [cheqd community](https://discord.gg/cheqd) for support and discussions
