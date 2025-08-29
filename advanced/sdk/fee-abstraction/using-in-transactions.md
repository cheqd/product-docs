# Using Fee Abstraction in Transactions

This guide demonstrates how to use the fee abstraction module with the cheqd SDK for both standard Cosmos SDK transactions and identity transactions (DIDs and DID-Linked Resources).

Fee abstraction allows users to pay transaction fees in alternative tokens (like IBC tokens from other chains) instead of the native CHEQ token, making the network more accessible to users from different ecosystems.

## Prerequisites

Before using fee abstraction, ensure you have:

1. [Installed and configured the cheqd SDK](../sdk-setup.md)
2. A funded wallet with the alternative token for fee payment
3. Access to a cheqd network with fee abstraction enabled
4. Understanding of the available host zones and supported tokens
5. **Important**: Your account must be funded with the bridged IBC representation of assets (e.g., transferred from Osmosis to cheqd) for fee abstraction to work

> **Note**: Fee amounts in examples require manual conversion from CHEQ equivalent at current exchange rates. An upcoming SDK release will introduce USD fixed pricing and built-in price aggregation for automated fee calculation.

## SDK Initialization with Fee Abstraction

```typescript
import { 
  createCheqdSDK, 
  DIDModule,
  ResourceModule,
  FeeabstractionModule,
  FeemarketModule,
  CheqdNetwork,
  AbstractCheqdSDKModule,
  type ICheqdSDKOptions 
} from '@cheqd/sdk';
import { DirectSecp256k1HdWallet } from '@cosmjs/proto-signing';

async function initializeSDKWithFeeAbstraction() {
  const wallet = await DirectSecp256k1HdWallet.fromMnemonic(
    'your twelve word mnemonic phrase goes here like this example', 
    { prefix: 'cheqd' }
  );

  const options: ICheqdSDKOptions = {
    modules: [
      DIDModule as unknown as AbstractCheqdSDKModule,
      ResourceModule as unknown as AbstractCheqdSDKModule,
      FeeabstractionModule as unknown as AbstractCheqdSDKModule,
      FeemarketModule as unknown as AbstractCheqdSDKModule
    ],
    rpcUrl: 'https://rpc.cheqd.network',
    network: CheqdNetwork.Testnet,
    wallet: wallet
  };

  const sdk = await createCheqdSDK(options);
  
  return { sdk, wallet };
}
```

## Standard Cosmos SDK Transactions

### Token Transfer (Bank Module)

```typescript
import { MsgSend } from 'cosmjs-types/cosmos/bank/v1beta1/tx';
import { coin } from '@cosmjs/stargate';

async function transferTokensWithFeeAbstraction() {
  const { sdk, wallet } = await initializeSDKWithFeeAbstraction();
  
  const sender = (await wallet.getAccounts())[0].address;
  const recipient = 'cheqd1recipient...';
  
  // Create transfer message
  const transferMsg = {
    typeUrl: '/cosmos.bank.v1beta1.MsgSend',
    value: MsgSend.fromPartial({
      fromAddress: sender,
      toAddress: recipient,
      amount: [coin('1000000', 'ncheq')] // 1 CHEQ
    })
  };

  // Define fee in alternative token (e.g., IBC USDC)
  // Note: Fee amounts require manual conversion from CHEQ equivalent at current exchange rates
  // Upcoming SDK release will introduce USD fixed pricing and built-in price aggregation
  // Ensure your account is funded with the bridged asset (IBC representation) on cheqd network
  const feeInIbcUsdc = {
    amount: [coin('5000', 'ibc/F663521BF1836B00F5F177680F74BFB9A8B5654A694D0D2BC249E03CF2509013')], // IBC USDC
    gas: '200000',
    payer: sender
  };

  try {
    const result = await sdk.signer.signAndBroadcast(
      sender,
      [transferMsg],
      feeInIbcUsdc,
      'Transfer with IBC USDC fee'
    );

    if (result.code === 0) {
      console.log('✅ Transfer successful with IBC USDC fee payment!');
      console.log(`Transaction hash: ${result.transactionHash}`);
      console.log(`Gas used: ${result.gasUsed}`);
    } else {
      console.error(`❌ Transfer failed: ${result.rawLog}`);
    }
  } catch (error) {
    console.error('Error during transfer:', error);
  }
}
```

### Staking Delegation

```typescript
import { MsgDelegate } from 'cosmjs-types/cosmos/staking/v1beta1/tx';

async function delegateWithFeeAbstraction() {
  const { sdk, wallet } = await initializeSDKWithFeeAbstraction();
  
  const delegator = (await wallet.getAccounts())[0].address;
  const validator = 'cheqdvaloper1validator...';
  
  // Create delegation message
  const delegateMsg = {
    typeUrl: '/cosmos.staking.v1beta1.MsgDelegate',
    value: MsgDelegate.fromPartial({
      delegatorAddress: delegator,
      validatorAddress: validator,
      amount: coin('10000000', 'ncheq') // 10 CHEQ
    })
  };

  // Use FeemarketModule to get current gas prices for fee calculation
  const feemarket = new FeemarketModule(sdk.signer, sdk.querier);
  
  try {
    // Get dynamic gas price for IBC OSMO
    const gasPrice = await feemarket.generateSafeGasPriceByDenom(
      'ibc/ED07A3391A112B175915CD8FAF43A2DA8E4790EDE12566649D0C2F97716B8518' // IBC OSMO
    );
    
    // Generate fee from gas price
    // Note: Price conversion is handled automatically by FeemarketModule
    // Ensure your account is funded with the bridged IBC OSMO on cheqd network
    // Upcoming SDK release will introduce USD fixed pricing and built-in price aggregation
    const fee = FeemarketModule.generateFeesFromGasPrice(gasPrice, delegator, '250000');

    const result = await sdk.signer.signAndBroadcast(
      delegator,
      [delegateMsg],
      fee,
      'Delegation with IBC OSMO fee'
    );

    if (result.code === 0) {
      console.log('✅ Delegation successful with IBC OSMO fee payment!');
      console.log(`Transaction hash: ${result.transactionHash}`);
      console.log(`Gas used: ${result.gasUsed}`);
    } else {
      console.error(`❌ Delegation failed: ${result.rawLog}`);
    }
  } catch (error) {
    console.error('Error during delegation:', error);
  }
}
```

### Claiming Rewards

```typescript
import { MsgWithdrawDelegatorReward } from 'cosmjs-types/cosmos/distribution/v1beta1/tx';

async function claimRewardsWithFeeAbstraction() {
  const { sdk, wallet } = await initializeSDKWithFeeAbstraction();
  
  const delegator = (await wallet.getAccounts())[0].address;
  const validators = [
    'cheqdvaloper1validator1...',
    'cheqdvaloper1validator2...',
    'cheqdvaloper1validator3...'
  ];
  
  // Create reward claim messages for multiple validators
  const claimMessages = validators.map(validator => ({
    typeUrl: '/cosmos.distribution.v1beta1.MsgWithdrawDelegatorReward',
    value: MsgWithdrawDelegatorReward.fromPartial({
      delegatorAddress: delegator,
      validatorAddress: validator
    })
  }));

  // Define fee in alternative token (e.g., IBC EURe)
  // Note: Fee amounts require manual conversion from CHEQ equivalent at current exchange rates
  // Upcoming SDK release will introduce USD fixed pricing and built-in price aggregation
  // Ensure your account is funded with the bridged IBC EURe on cheqd network
  const feeInIbcEure = {
    amount: [coin('8000', 'ibc/5973C068568365FFF40DEDCF1A1CB7582B6116B731CD31A12231AE25E20B871')], // IBC EURe
    gas: '300000', // Higher gas for multiple messages
    payer: delegator
  };

  try {
    const result = await sdk.signer.signAndBroadcast(
      delegator,
      claimMessages,
      feeInIbcEure,
      'Claim rewards with IBC EURe fee'
    );

    if (result.code === 0) {
      console.log('✅ Rewards claimed successfully with IBC EURe fee payment!');
      console.log(`Transaction hash: ${result.transactionHash}`);
      console.log(`Gas used: ${result.gasUsed}`);
      console.log(`Claimed from ${validators.length} validators`);
    } else {
      console.error(`❌ Claim rewards failed: ${result.rawLog}`);
    }
  } catch (error) {
    console.error('Error during reward claim:', error);
  }
}
```

## Identity Transactions (DID and DLR)

### DID Creation with Fee Abstraction

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
import { toString, fromString } from 'uint8arrays';

async function createDidWithFeeAbstraction() {
  const { sdk, wallet } = await initializeSDKWithFeeAbstraction();
  
  // Generate key pair and create DID payload
  const keyPair = createKeyPairBase64();
  const verificationKeys = createVerificationKeys(
    keyPair.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-1'
  );
  const verificationMethods = createDidVerificationMethod(
    [VerificationMethods.Ed255192020],
    [verificationKeys]
  );
  const didPayload = createDidPayload(verificationMethods, [verificationKeys]);

  // Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: didPayload.verificationMethod![0].id,
      privateKeyHex: toString(fromString(keyPair.privateKey, 'base64'), 'hex'),
    },
  ];

  const feePayer = (await wallet.getAccounts())[0].address;
  
  try {
    // Use IBC token for fee payment instead of native CHEQ
    // Note: Fee amounts require manual conversion from CHEQ equivalent at current exchange rates
    // Upcoming SDK release will introduce USD fixed pricing and built-in price aggregation
    // Ensure your account is funded with the bridged IBC USDC on cheqd network
    const feeInIbcUsdc = {
      amount: [coin('12000', 'ibc/F663521BF1836B00F5F177680F74BFB9A8B5654A694D0D2BC249E03CF2509013')], // IBC USDC
      gas: '360000', // Standard gas limit for DID creation
      payer: feePayer
    };

    const result = await sdk.createDidDocTx(
      signInputs, 
      didPayload, 
      feePayer, 
      feeInIbcUsdc,
      'DID creation with IBC USDC fee'
    );

    if (result.code === 0) {
      console.log('✅ DID created successfully with IBC USDC fee payment!');
      console.log(`DID: ${didPayload.id}`);
      console.log(`Transaction hash: ${result.transactionHash}`);
      console.log(`Gas used: ${result.gasUsed}`);
    } else {
      console.error(`❌ DID creation failed: ${result.rawLog}`);
    }

    return { didPayload, result };
  } catch (error) {
    console.error('Error creating DID:', error);
    throw error;
  }
}
```

### DID Update with Fee Abstraction

```typescript
async function updateDidWithFeeAbstraction(existingDidPayload: any) {
  const { sdk, wallet } = await initializeSDKWithFeeAbstraction();
  
  // Add a new verification method to the existing DID
  const newKeyPair = createKeyPairBase64();
  const newVerificationKeys = createVerificationKeys(
    newKeyPair.publicKey,
    MethodSpecificIdAlgo.Base58,
    'key-2' // New key identifier
  );
  const newVerificationMethods = createDidVerificationMethod(
    [VerificationMethods.Ed255192020],
    [newVerificationKeys]
  );

  // Create updated DID payload
  const updatedDidPayload = {
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

  // Sign with existing key (required for updates)
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: existingDidPayload.verificationMethod[0].id,
      privateKeyHex: 'existing-key-private-key-hex',
    },
  ];

  const feePayer = (await wallet.getAccounts())[0].address;
  
  try {
    // Use IBC token for fee payment instead of native CHEQ
    // Note: Fee amounts require manual conversion from CHEQ equivalent at current exchange rates
    // Upcoming SDK release will introduce USD fixed pricing and built-in price aggregation
    // Ensure your account is funded with the bridged IBC USDC on cheqd network
    const feeInIbcUsdc = {
      amount: [coin('15000', 'ibc/F663521BF1836B00F5F177680F74BFB9A8B5654A694D0D2BC249E03CF2509013')], // IBC USDC
      gas: '360000', // Standard gas limit for DID updates
      payer: feePayer
    };

    const result = await sdk.updateDidDocTx(
      signInputs,
      updatedDidPayload,
      feePayer,
      feeInIbcUsdc,
      'DID update with IBC USDC fee'
    );

    if (result.code === 0) {
      console.log('✅ DID updated successfully with IBC USDC fee payment!');
      console.log(`DID: ${updatedDidPayload.id}`);
      console.log(`Added new verification method: key-2`);
      console.log(`Transaction hash: ${result.transactionHash}`);
      console.log(`Gas used: ${result.gasUsed}`);
    } else {
      console.error(`❌ DID update failed: ${result.rawLog}`);
    }

    return result;
  } catch (error) {
    console.error('Error updating DID:', error);
    throw error;
  }
}
```

### DID-Linked Resource Creation with Fee Abstraction

```typescript
import { 
  createResourcePayload,
  v4 as uuidv4,
  type MsgCreateResourcePayload
} from '@cheqd/sdk';

async function createResourceWithFeeAbstraction(collectionId: string) {
  const { sdk, wallet } = await initializeSDKWithFeeAbstraction();
  
  // Create JSON schema resource
  const schemaContent = {
    "$schema": "https://json-schema.org/draft/2020-12/schema",
    "$id": "https://example.com/user.schema.json",
    "title": "User",
    "type": "object",
    "properties": {
      "name": { "type": "string" },
      "email": { "type": "string", "format": "email" },
      "age": { "type": "integer", "minimum": 0 }
    },
    "required": ["name", "email"]
  };

  const data = new TextEncoder().encode(JSON.stringify(schemaContent, null, 2));
  const resourceId = uuidv4();

  // Create resource payload
  const resourcePayload: MsgCreateResourcePayload = {
    collectionId,
    id: resourceId,
    name: 'User Schema',
    resourceType: 'JSONSchema',
    mediaType: 'application/json',
    data,
    version: '1.0.0',
    alsoKnownAs: [{
      uri: `https://schemas.example.com/${resourceId}`,
      description: 'Schema Registry Location'
    }]
  };

  // Create sign inputs
  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-did-controller-private-key-hex',
    },
  ];

  const feePayer = (await wallet.getAccounts())[0].address;
  
  try {
    // Use IBC token for fee payment instead of native CHEQ
    // Note: Fee amounts require manual conversion from CHEQ equivalent at current exchange rates
    // Upcoming SDK release will introduce USD fixed pricing and built-in price aggregation
    // Ensure your account is funded with the bridged IBC EURe on cheqd network
    const feeInIbcEure = {
      amount: [coin('25000', 'ibc/5973C068568365FFF40DEDCF1A1CB7582B6116B731CD31A12231AE25E20B871')], // IBC EURe
      gas: '2000000', // Standard gas limit for JSON resource creation
      payer: feePayer
    };

    const result = await sdk.createResourceTx(
      signInputs,
      resourcePayload,
      feePayer,
      feeInIbcEure,
      'Resource creation with IBC EURe fee'
    );

    if (result.code === 0) {
      console.log('✅ DID-Linked Resource created successfully with IBC EURe fee payment!');
      console.log(`Resource ID: ${resourcePayload.id}`);
      console.log(`Resource Name: ${resourcePayload.name}`);
      console.log(`Transaction hash: ${result.transactionHash}`);
      console.log(`Gas used: ${result.gasUsed}`);
    } else {
      console.error(`❌ Resource creation failed: ${result.rawLog}`);
    }

    return { resourcePayload, result };
  } catch (error) {
    console.error('Error creating resource:', error);
    throw error;
  }
}
```

### Image Resource Creation with Fee Abstraction

```typescript
import { fromString } from 'uint8arrays';

async function createImageResourceWithFeeAbstraction(collectionId: string) {
  const { sdk, wallet } = await initializeSDKWithFeeAbstraction();
  
  // Example base64 encoded PNG image (1x1 pixel)
  const imageBase64 = 'iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVR42mP8/5+hHgAHggJ/PchI7wAAAABJRU5ErkJggg==';
  const imageData = fromString(imageBase64, 'base64');
  
  const resourceId = uuidv4();

  // Create resource payload for image
  const resourcePayload: MsgCreateResourcePayload = {
    collectionId,
    id: resourceId,
    name: 'Profile Avatar',
    resourceType: 'Image',
    mediaType: 'image/png',
    data: imageData,
    version: '1.0.0',
    alsoKnownAs: [{
      uri: `https://cdn.example.com/images/${resourceId}`,
      description: 'CDN Location'
    }]
  };

  const signInputs: ISignInputs[] = [
    {
      verificationMethodId: `${collectionId}#key-1`,
      privateKeyHex: 'your-did-controller-private-key-hex',
    },
  ];

  const feePayer = (await wallet.getAccounts())[0].address;
  
  try {
    // Use IBC token for fee payment instead of native CHEQ
    // Note: Fee amounts require manual conversion from CHEQ equivalent at current exchange rates
    // Upcoming SDK release will introduce USD fixed pricing and built-in price aggregation
    // Ensure your account is funded with the bridged IBC USDC on cheqd network
    const feeInIbcUsdc = {
      amount: [coin('35000', 'ibc/F663521BF1836B00F5F177680F74BFB9A8B5654A694D0D2BC249E03CF2509013')], // IBC USDC
      gas: '2000000', // Standard gas limit for image resource creation (higher cost)
      payer: feePayer
    };

    const result = await sdk.createResourceTx(
      signInputs,
      resourcePayload,
      feePayer,
      feeInIbcUsdc,
      'Image resource creation with IBC USDC fee'
    );

    if (result.code === 0) {
      console.log('✅ Image resource created successfully with IBC USDC fee payment!');
      console.log(`Resource ID: ${resourcePayload.id}`);
      console.log(`Resource Name: ${resourcePayload.name}`);
      console.log(`Image size: ${imageData.length} bytes`);
      console.log(`Transaction hash: ${result.transactionHash}`);
      console.log(`Gas used: ${result.gasUsed}`);
    } else {
      console.error(`❌ Image resource creation failed: ${result.rawLog}`);
    }

    return { resourcePayload, result };
  } catch (error) {
    console.error('Error creating image resource:', error);
    throw error;
  }
}
```

## Fee Abstraction Management

### Query Available Host Zones and Supported IBC Tokens

```typescript
async function queryAvailableHostZones() {
  const { sdk } = await initializeSDKWithFeeAbstraction();
  
  try {
    const feeabsModule = new FeeabstractionModule(sdk.signer, sdk.querier);
    
    console.log('📋 Querying supported IBC tokens for fee abstraction...');
    
    // Query the host zone configurations from the fee abstraction module
    // This returns the actual supported IBC denominations and their configurations
    const hostZoneConfigs = await feeabsModule.queryAllHostZoneConfig();
    
    console.log('✅ Supported IBC tokens for fee abstraction:');
    console.log('');
    
    const supportedTokens: any[] = [];
    
    for (const config of hostZoneConfigs) {
      if (config.enabled) {
        const tokenInfo = {
          chainId: config.chainId,
          hostDenom: config.hostDenom,
          ibcDenom: config.ibcDenom,
          connectionId: config.connectionId,
          transferChannel: config.transferChannelId,
          minSwapAmount: config.minSwapAmount,
          enabled: config.enabled
        };
        
        supportedTokens.push(tokenInfo);
        
        console.log(`🔗 ${config.hostDenom.toUpperCase()}:`);
        console.log(`   Chain ID: ${config.chainId}`);
        console.log(`   IBC Denom: ${config.ibcDenom}`);
        console.log(`   Host Denom: ${config.hostDenom}`);
        console.log(`   Connection ID: ${config.connectionId}`);
        console.log(`   Transfer Channel: ${config.transferChannelId}`);
        console.log(`   Min Swap Amount: ${config.minSwapAmount}`);
        console.log('');
      }
    }
    
    // Helper function to get IBC denom by host denomination
    const getIbcDenomByHostDenom = (hostDenom: string) => {
      const token = supportedTokens.find(t => 
        t.hostDenom.toLowerCase() === hostDenom.toLowerCase()
      );
      return token?.ibcDenom;
    };
    
    // Helper function to validate if an IBC denom is supported
    const isIbcDenomSupported = (ibcDenom: string) => {
      return supportedTokens.some(token => token.ibcDenom === ibcDenom);
    };
    
    console.log('💡 Usage examples:');
    if (supportedTokens.length > 0) {
      supportedTokens.slice(0, 3).forEach(token => {
        console.log(`   ${token.hostDenom.toUpperCase()} fee: coin('5000', '${token.ibcDenom}')`);
      });
    }
    
    return {
      supportedTokens,
      getIbcDenomByHostDenom,
      isIbcDenomSupported
    };
  } catch (error) {
    console.error('Error querying host zones:', error);
    
    // Fallback to example configuration if query fails
    console.log('⚠️  Using fallback configuration (for development)');
    
    const fallbackTokens = [
      {
        chainId: 'noble-1',
        hostDenom: 'uusdc',
        ibcDenom: 'ibc/F663521BF1836B00F5F177680F74BFB9A8B5654A694D0D2BC249E03CF2509013',
        connectionId: 'connection-0',
        transferChannel: 'channel-0',
        minSwapAmount: '1000',
        enabled: true
      },
      {
        chainId: 'noble-1',
        hostDenom: 'aeure',
        ibcDenom: 'ibc/5973C068568365FFF40DEDCF1A1CB7582B6116B731CD31A12231AE25E20B871',
        connectionId: 'connection-1',
        transferChannel: 'channel-1',
        minSwapAmount: '1000',
        enabled: true
      },
      {
        chainId: 'osmosis-1',
        hostDenom: 'uosmo',
        ibcDenom: 'ibc/ED07A3391A112B175915CD8FAF43A2DA8E4790EDE12566649D0C2F97716B8518',
        connectionId: 'connection-2',
        transferChannel: 'channel-2',
        minSwapAmount: '1000',
        enabled: true
      }
    ];
    
    return {
      supportedTokens: fallbackTokens,
      getIbcDenomByHostDenom: (hostDenom: string) => 
        fallbackTokens.find(t => t.hostDenom === hostDenom)?.ibcDenom,
      isIbcDenomSupported: (ibcDenom: string) => 
        fallbackTokens.some(t => t.ibcDenom === ibcDenom)
    };
  }
}

// Helper function to validate IBC denom is supported
async function validateIbcDenomSupported(ibcDenom: string): Promise<boolean> {
  const { isIbcDenomSupported } = await queryAvailableHostZones();
  return isIbcDenomSupported(ibcDenom);
}

// Example usage of host zone query in transaction preparation
async function prepareTransactionWithValidatedFee(
  messages: any[], 
  preferredToken: string = 'uatom'
) {
  const { sdk, wallet } = await initializeSDKWithFeeAbstraction();
  const { supportedTokens, getIbcDenomByHostDenom } = await queryAvailableHostZones();
  
  const feePayer = (await wallet.getAccounts())[0].address;
  
  // Get the IBC denomination for the preferred token
  const ibcDenom = getIbcDenomByHostDenom(preferredToken);
  
  if (!ibcDenom) {
    throw new Error(`Token ${preferredToken} is not supported for fee abstraction`);
  }
  
  // Prepare fee with validated IBC denom
  const fee = {
    amount: [coin('10000', ibcDenom)],
    gas: '300000',
    payer: feePayer
  };
  
  console.log(`✅ Using ${preferredToken.toUpperCase()} for transaction fee`);
  console.log(`   IBC Denom: ${ibcDenom}`);
  
  return { fee, ibcDenom, feePayer };
}
```

## Complete Working Example

Here's a complete example that demonstrates the full workflow:

```typescript
import { 
  createCheqdSDK, 
  DIDModule, 
  ResourceModule, 
  FeeabstractionModule,
  FeemarketModule,
  CheqdNetwork,
  AbstractCheqdSDKModule,
  createDidPayload,
  createDidVerificationMethod,
  createKeyPairBase64,
  createVerificationKeys,
  MethodSpecificIdAlgo,
  VerificationMethods,
  type ISignInputs,
  type ICheqdSDKOptions
} from '@cheqd/sdk';
import { DirectSecp256k1HdWallet } from '@cosmjs/proto-signing';
import { coin } from '@cosmjs/stargate';
import { MsgSend } from 'cosmjs-types/cosmos/bank/v1beta1/tx';
import { toString, fromString } from 'uint8arrays';

async function completeWorkflowWithFeeAbstraction() {
  try {
    // Initialize SDK with fee abstraction support
    console.log('🚀 Initializing SDK with fee abstraction...');
    
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(
      'abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon about', 
      { prefix: 'cheqd' }
    );

    const sdk = await createCheqdSDK({
      modules: [
        DIDModule as unknown as AbstractCheqdSDKModule,
        ResourceModule as unknown as AbstractCheqdSDKModule,
        FeeabstractionModule as unknown as AbstractCheqdSDKModule,
        FeemarketModule as unknown as AbstractCheqdSDKModule
      ],
      rpcUrl: 'https://rpc.cheqd.network',
      network: CheqdNetwork.Testnet,
      wallet: wallet
    });

    const senderAddress = (await wallet.getAccounts())[0].address;
    
    // Step 1: Query available host zones
    console.log('📋 Checking available fee abstraction options...');
    await queryAvailableHostZones();
    
    // Step 2: Standard Cosmos transaction with fee abstraction
    console.log('💸 Performing token transfer with IBC USDC fee...');
    // Note: Fee amounts require manual conversion from CHEQ equivalent at current exchange rates
    // Upcoming SDK release will introduce USD fixed pricing and built-in price aggregation
    // Ensure your account is funded with the bridged IBC USDC on cheqd network
    
    const transferMsg = {
      typeUrl: '/cosmos.bank.v1beta1.MsgSend',
      value: MsgSend.fromPartial({
        fromAddress: senderAddress,
        toAddress: 'cheqd1recipient...', // Replace with actual recipient
        amount: [coin('100000', 'ncheq')] // 0.1 CHEQ
      })
    };

    const transferFeeInIbcUsdc = {
      amount: [coin('5000', 'ibc/F663521BF1836B00F5F177680F74BFB9A8B5654A694D0D2BC249E03CF2509013')],
      gas: '200000',
      payer: senderAddress
    };

    const transferResult = await sdk.signer.signAndBroadcast(
      senderAddress,
      [transferMsg],
      transferFeeInIbcUsdc,
      'Transfer with IBC USDC fee'
    );

    if (transferResult.code === 0) {
      console.log('✅ Transfer successful!');
      console.log(`  Hash: ${transferResult.transactionHash}`);
    }
    
    // Step 3: Create DID with fee abstraction  
    console.log('🆔 Creating DID with IBC USDC fee...');
    
    const keyPair = createKeyPairBase64();
    const verificationKeys = createVerificationKeys(
      keyPair.publicKey,
      MethodSpecificIdAlgo.Base58,
      'key-1'
    );
    const verificationMethods = createDidVerificationMethod(
      [VerificationMethods.Ed255192020],
      [verificationKeys]
    );
    const didPayload = createDidPayload(verificationMethods, [verificationKeys]);

    const didSignInputs: ISignInputs[] = [
      {
        verificationMethodId: didPayload.verificationMethod![0].id,
        privateKeyHex: toString(fromString(keyPair.privateKey, 'base64'), 'hex'),
      },
    ];

    // Note: Fee amounts require manual conversion from CHEQ equivalent at current exchange rates
    // Upcoming SDK release will introduce USD fixed pricing and built-in price aggregation
    // Ensure your account is funded with the bridged IBC USDC on cheqd network
    const didFeeInIbcUsdc = {
      amount: [coin('15000', 'ibc/F663521BF1836B00F5F177680F74BFB9A8B5654A694D0D2BC249E03CF2509013')],
      gas: '360000',
      payer: senderAddress
    };

    const didResult = await sdk.createDidDocTx(
      didSignInputs,
      didPayload,
      senderAddress,
      didFeeInIbcUsdc,
      'DID creation with IBC USDC fee'
    );

    if (didResult.code === 0) {
      console.log('✅ DID created successfully!');
      console.log(`  DID: ${didPayload.id}`);
      console.log(`  Hash: ${didResult.transactionHash}`);
    }

    // Step 4: Create DID-Linked Resource with fee abstraction
    console.log('📄 Creating DID-Linked Resource with IBC JUNO fee...');
    
    const resourceContent = {
      "name": "Sample Schema",
      "type": "object",
      "properties": {
        "id": { "type": "string" },
        "name": { "type": "string" }
      }
    };

    const resourceData = new TextEncoder().encode(JSON.stringify(resourceContent, null, 2));
    const collectionId = didPayload.id.split(':').pop()!;

    const resourcePayload = {
      collectionId,
      id: 'resource-uuid-example',
      name: 'Sample Schema',
      resourceType: 'JSONSchema',
      mediaType: 'application/json',
      data: resourceData,
      version: '1.0.0'
    };

    const resourceSignInputs: ISignInputs[] = [
      {
        verificationMethodId: didPayload.verificationMethod![0].id,
        privateKeyHex: toString(fromString(keyPair.privateKey, 'base64'), 'hex'),
      },
    ];

    // Note: Fee amounts require manual conversion from CHEQ equivalent at current exchange rates
    // Upcoming SDK release will introduce USD fixed pricing and built-in price aggregation
    // Ensure your account is funded with the bridged IBC EURe on cheqd network
    const resourceFeeInIbcEure = {
      amount: [coin('25000', 'ibc/5973C068568365FFF40DEDCF1A1CB7582B6116B731CD31A12231AE25E20B871')],
      gas: '2000000',
      payer: senderAddress
    };

    const resourceResult = await sdk.createResourceTx(
      resourceSignInputs,
      resourcePayload,
      senderAddress,
      resourceFeeInIbcEure,
      'Resource creation with IBC EURe fee'
    );

    if (resourceResult.code === 0) {
      console.log('✅ DID-Linked Resource created successfully!');
      console.log(`  Resource ID: ${resourcePayload.id}`);
      console.log(`  Hash: ${resourceResult.transactionHash}`);
    }

    console.log('🎉 Complete workflow with fee abstraction finished successfully!');
    
    return {
      transferResult,
      didResult,
      resourceResult,
      didPayload,
      resourcePayload
    };

  } catch (error) {
    console.error('❌ Workflow failed:', error);
    throw error;
  }
}

// Execute the complete workflow
completeWorkflowWithFeeAbstraction()
  .then(results => {
    console.log('All operations completed successfully!');
  })
  .catch(error => {
    console.error('Workflow error:', error);
  });
```

## Best Practices

### Fee Management

1. **Token Selection**: Choose widely accepted IBC tokens with good liquidity
2. **Fee Estimation**: Use dynamic fee calculation based on current market rates
3. **Fallback Strategy**: Always have native CHEQ tokens as a backup payment method
4. **Cost Monitoring**: Monitor fee costs across different tokens to optimize expenses

### Transaction Optimization

1. **Batch Operations**: Group multiple messages to reduce fee overhead
2. **Gas Estimation**: Use accurate gas estimation to avoid overpaying
3. **Retry Logic**: Implement retry mechanisms for failed transactions
4. **Fee Calculation**: Use FeemarketModule for dynamic fee calculation

### Security Considerations

1. **Private Key Management**: Securely store and handle private keys
2. **Transaction Validation**: Validate transaction parameters before signing
3. **Network Selection**: Use appropriate network (testnet for development)
4. **Error Handling**: Implement comprehensive error handling

## Troubleshooting

### Common Issues

1. **Insufficient Funds**: Ensure wallet has enough alternative tokens for fees
2. **Invalid IBC Denom**: Verify IBC denomination is correct for the target chain
3. **Gas Estimation**: Use appropriate gas limits for different transaction types
4. **Host Zone Configuration**: Ensure the alternative token's host zone is properly configured

### Error Resolution

```typescript
// Example error handling for fee abstraction
async function handleFeeAbstractionError(error: any) {
  if (error.message.includes('insufficient funds')) {
    console.error('❌ Insufficient funds in alternative token');
    console.log('💡 Try using native CHEQ tokens or fund your wallet with the required token');
  } else if (error.message.includes('invalid denom')) {
    console.error('❌ Invalid IBC denomination');
    console.log('💡 Check available host zones and use correct IBC denom');
  } else if (error.message.includes('gas')) {
    console.error('❌ Gas-related error');
    console.log('💡 Increase gas limit or check gas price calculation');
  } else {
    console.error('❌ Unknown error:', error);
  }
}
```

## Next Steps

After implementing fee abstraction:

- [Explore advanced fee market dynamics](../feemarket-module/dynamic-fees.md)
- [Learn about cross-chain resource management](../resource-module/cross-chain-resources.md)
- Monitor fee costs and optimize token selection
- Implement automated fee strategy based on market conditions

## Getting Help

If you encounter issues:

- Check the [cheqd SDK GitHub repository](https://github.com/cheqd/sdk) for updates
- Review the [fee abstraction module documentation](https://docs.osmosis.zone/overview/features/fee-abstraction/)
- Join the [cheqd Community Discord](https://discord.gg/cheqd) for support
- Consult the [Cosmos SDK documentation](https://docs.cosmos.network/) for standard transaction types
