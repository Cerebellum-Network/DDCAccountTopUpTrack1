



Cere <> School 42 Hackathon



# 🚀 **Quick Start Guide for Track 1: DDC Account Top-Up System**

## 📋 Table of Contents

1. [Introduction](#-introduction)
2. [Quickstart Guide](#-quickstart-guide)
3. [Milestones and Acceptance Criteria](#-milestones-and-acceptance-criteria)
4. [Library Integration](#-library-integration)
5. [CERE Token Faucet](#-cere-token-faucet)
6. [Submission Checklist](#-submission-checklist)

## 🌟 Introduction

Welcome to the Hackathon! This project aims to enhance the user experience for topping up DDC (Decentralized Data Cloud) Accounts through a streamlined process that integrates Ethereum wallets with the Cere Mainnet. By leveraging the capabilities of Hyper Bridge and smart contracts, we will create an efficient and user-friendly top-up mechanism.

### New User Experience:

1. Users initiate the top-up process from the Cere Developer Console.
2. They specify the amount of CERE tokens they wish to transfer.
3. MetaMask is used to sign the transaction, which is then sent to the TokenGateway contract.
4. The TokenGateway contract's `teleport` function is called with specific parameters.
5. Once the transaction is confirmed on the Ethereum network, the Hyper Bridge relays this information to the Cere Mainnet.
6. The system then mints equivalent tokens on Cere Mainnet and transfers them to the user's DDC Account.

## 🏁 Quickstart Guide

### 🛠️ Setup

1. Fork the project repository.
2. Set up your development environment with whatever framework you like, such as React.js.
3. Integrate MetaMask using the [official documentation](https://docs.metamask.io/sdk/).

### 🔑 Key Resources

- CERE ERC Token Address (Sepolia): [0xf310641b4b6c032d0c88d72d712c020fca9805a3](https://sepolia.etherscan.io/token/0xf310641b4b6c032d0c88d72d712c020fca9805a3)
- TokenGateway Address (Sepolia): [0xFcDa26cA021d5535C3059547390E6cCd8De7acA6](https://sepolia.etherscan.io/address/0xFcDa26cA021d5535C3059547390E6cCd8De7acA6#writeContract)
- Cere Testnet Explorer: [https://explorer.cere.network/?rpc=wss%3A%2F%2Farchive.testnet.cere.network%2Fws#/explorer](https://explorer.cere.network/?rpc=wss%3A%2F%2Farchive.testnet.cere.network%2Fws#/explorer)

### 📝 Implementation Steps

1. **Connect to MetaMask**: Implement wallet connection functionality.

2. **Execute the Teleport**:
    - Go to the teleport contract and call the **teleport** function at [TokenGateway Address](https://testnet.bscscan.com/address/0xFcDa26cA021d5535C3059547390E6cCd8De7acA6#writeContract) with the following inputs:
        1. **teleport**: `0`
        2. **amount**: Specify the token amount to transfer.
            - Example: For transferring 800 CERE, enter: `800000000000000000000` (800 followed by 18 zeros).
        3. **relayerFee**: `0`
        4. **assetId**: Use the Asset ID retrieved in Step 3 (include the 0x prefix). `0xac05b69379f7ac8d594d29d1cc11e6ed5bec3b481c0882bbb1c4fdaa08ba77c6`
        5. **redeem**: `false`
        6. **to**: Enter the 32-byte hex public key of the **Substrate account** (not the SS58 address).
        7. **dest**: `0x5355425354524154452d63657265` (Hex representation of `SUBSTRATE-cere`).
        8. **timeout**: `0`
        9. **nativeCost**: `0`

        10. **data**: Encoded call of the `deposit_extra` function in the DDC customers pallet.

            To encode the call, you should use the SCALE codec. The encoded data should include:
            - The pallet index for the DDC customers pallet i.e., 36
            - The call index for the `deposit_extra` function within that pallet i.e., 2
            - The encoded arguments for the `deposit_extra` function.

            - Here's how to properly structure the data parameter:

            1. Encode the call data:

            ```javascript
            import { u8aToHex } from '@polkadot/util';
            import { createType } from '@polkadot/types';
   
            const PALLET_INDEX = 36;
            const CALL_INDEX = 2;
   
            const amount = 10000000000n; // 1 CERE in its smallest unit
            const accountId = '0x...'; // The account ID to top up
   
            const call = createType('Call', {
              callIndex: [PALLET_INDEX, CALL_INDEX],
              args: {
                amount,
                account_id: accountId
              }
            });
   
            const encodedCall = call.toU8a();
            ```

            2. Sign the encoded call:

            ```javascript
            import { Keyring } from '@polkadot/keyring';
   
            const keyring = new Keyring({ type: 'sr25519' });
            const account = keyring.addFromUri('your_seed_or_private_key');
            const signature = account.sign(encodedCall);
            ```

            3. Combine the encoded call and signature:

            ```javascript
            const substrateData = {
              runtime_call: encodedCall,
              signature: signature
            };
   
            const encodedSubstrateData = createType('SubstrateCalldata', substrateData).encode();
            const data = u8aToHex(encodedSubstrateData);
            ```

Now you can use this `data` value as input for the teleport function's data parameter.

3. **Track Transaction**: Monitor the Cere Testnet for corresponding events (approximately 20 minutes after initiating transfer).

4. **Update UI**: Display transaction status and confirmation to users.

## 🎯 Milestones and Acceptance Criteria

### 🏆 MVP Milestones

1. **MetaMask Integration** 🦊
    - User can connect their MetaMask wallet to Developer Console.
    - UI displays connected wallet address and CERE token balance.

2. **Top-up Initiation** 💰
    - User can initiate a top-up transaction from Developer Console.
    - UI allows specifying amount of CERE tokens to transfer.

3. **Transaction Execution** 🔄
    - System interacts with TokenGateway Contract (Hyperbridge) using Ethers.js.
    - Transaction is properly signed and sent to Ethereum network.

4. **Transaction Monitoring** 👀
    - UI displays transaction status (Pending, Completed, Failed).
    - User receives confirmation upon successful transaction completion.

5. **DDC Account Update** 📊
    - System verifies transaction success on Cere Mainnet using Polkadot.js.
    - User's DDC Account balance is updated accordingly.

6. **Error Handling** ⚠️
    - System displays appropriate error messages for failed transactions.
    - Users receive clear instructions on resolving common issues.

### 🌈 Nice-To-Have Features

1. **Multi-Token Selection** 🔀
    - UI allows users to select from a list of supported ERC20 tokens for top-up.
    - Current exchange rate for selected token to CERE is displayed.

2. **Token Swap Integration** 💱
    - Successfully deploy and integrate a Token Swap Contract.
    - Users can approve token swap transactions from their MetaMask wallet.

3. **Transaction History** 📜
    - UI provides a history of both direct CERE top-ups and multi-token swaps.
    - Users can view details of past transactions, including original token, swap rate, and final CERE amount.

## 📚 Library Integration

### 🔗 Polkadot.js for Cere Testnet

Polkadot.js is official JavaScript API for interacting with Polkadot SDK-based chains, including Cere testnet.

#### Installation

```bash
npm install @polkadot/api
```

#### Usage

```javascript
import { ApiPromise, WsProvider } from '@polkadot/api';

async function connectToCere() {
  const wsProvider = new WsProvider('wss://archive.testnet.cere.network/ws');
  const api = await ApiPromise.create({ provider: wsProvider });
  console.log('Connected to Cere testnet:', api.genesisHash.toHex());
  return api;
}
```

#### Key Features

1. **Dynamic API Generation**: API automatically generates interfaces based on chain's metadata.
2. **Query Chain State**: Use `api.query` to read current chain state.
3. **Submit Transactions**: Use `api.tx` to submit extrinsics (transactions).
4. **Access Constants**: Use `api.consts` to access runtime constants.

### 🔗 Ethers.js for Sepolia Testnet

Ethers.js is a popular library for interacting with Ethereum-compatible networks, including Sepolia testnet.

#### Installation

```bash
npm install ethers
```

#### Usage

```javascript
import { ethers } from 'ethers';

async function connectToSepolia() {
  const provider = new ethers.JsonRpcProvider('https://rpc.sepolia.org');
  const network = await provider.getNetwork();
  console.log('Connected to Sepolia testnet:', network.name);
  return provider;
}
```

#### Key Features

1. **Wallet Management**: Create and manage Ethereum wallets securely.
2. **Contract Interaction**: Easily interact with smart contracts using their ABI.
3. **Transaction Handling**: Send transactions and monitor their status.
4. **Event Listening**: Subscribe to and handle blockchain events.

## 🚰 CERE Token Faucet

To obtain test CERE tokens, fill out provided form (link to be added).

## ✅ Submission Checklist

Before submitting your project, please ensure you've completed following milestones and tasks:

### MVP Milestones

- [ ] MetaMask Integration
    - [ ] Users can connect their MetaMask wallet to Developer Console
    - [ ] UI displays connected wallet address and CERE token balance

- [ ] Top-up Initiation
    - [ ] Users can initiate a top-up transaction from Developer Console
    - [ ] UI allows specifying amount of CERE tokens to transfer

- [ ] Transaction Execution
    - [ ] System interacts with TokenGateway Contract using Ethers.js
    - [ ] Transactions are properly signed and sent to Ethereum network

- [ ] Transaction Monitoring
    - [ ] UI displays transaction status (Pending, Completed, Failed)
    - [ ] Users receive confirmation upon successful transaction completion

- [ ] DDC Account Update
    - [ ] System verifies transaction success on Cere Mainnet using Polkadot.js
    - [ ] User's DDC Account balance is updated accordingly

- [ ] Error Handling
    - [ ] System displays appropriate error messages for failed transactions
    - [ ] Users receive clear instructions on resolving common issues

### Nice-To-Have Features (Optional)

- [ ] Multi-Token Selection
- [ ] Token Swap Integration
- [ ] Transaction History

### Final Steps

- [ ] Code is well-documented and follows best practices
- [ ] README file is updated with project details and setup instructions
- [ ] All dependencies are listed in package.json
- [ ] Project is tested on both Sepolia and Cere testnets
- [ ] Code is pushed to a public GitHub repository

Once you've completed checklist, please push your code to your GitHub repository and submit link along with any additional documentation or demo videos.

Good luck with your submission! 🍀

